# AI Security 5.3. Sensitive Information Disclosure

#### **Task 1 - Introduction**

Sensitive information disclosure in LLM applications occurs because sensitive or unprivileged data is introduced directly into the model's probabilistic context window—rather than through explicit model decision-making—exposing the systemic lack of inherent row-level access controls found in traditional database engines.

#### **Task 2 - OWASP LLM02**

Exposures from sensitive information disclosures are categorised under OWASP LLM02. They focus on protecting private data, proprietary logic, and confidential documents from being exposed through AI outputs.

![Explains how sensitive Info disclosure works internally](https://tryhackme-images.s3.amazonaws.com/user-uploads/69660b039266ef51dff2a92b/room-content/69660b039266ef51dff2a92b-1774341533914.png)

**Disclosure Is Different From Poisoning and Prompt Injection**

- Poisoning manipulates what the system learns.
- Prompt injection manipulates instructions at runtime.
- Sensitive Information Disclosure exposes data that already exists in the system.

The attacker does not need to modify the model. They only need to trigger retrieval or observe architectural weaknesses.

**How Data Leakage Happens in Real Systems:** Modern LLM applications often use Retrieval-Augmented Generation (RAG). Instead of relying only on training data, the system retrieves documents from a vector database and injects them into the model's context before generating a response. This creates multiple potential leakage points:

- Over-broad similarity search retrieves unrelated confidential documents
- Shared vector databases across departments or tenants
- Large context windows combining multiple document chunks
- Debug logging stores full prompts and retrieved content
- Weak or missing metadata filtering

If unauthorised data is retrieved, the exposure risk already exists even if the model only partially reveals it. The issue is architectural, not conversational.

**Scenario: Exposure Without Breaking Anything:** *A cloud infrastructure provider launches an AI-powered support assistant for customers. The assistant helps users troubleshoot deployment errors, billing issues, and configuration problems. The system uses RAG. It retrieves information from:*

- *Public documentation*
- *Internal troubleshooting playbooks*
- *Archived support tickets*
- *Incident postmortems*

*To improve answer quality, the engineering team decides to embed past support tickets into the vector database. These tickets often contain:*

- *Customer account IDs*
- *Deployment architecture diagrams*
- *API keys accidentally pasted during troubleshooting*
- *Root cause summaries*

*The team assumes this data is safe because the tickets are internal, the model is stateless, and the system prompt instructs the model not to reveal secrets.*

A customer asks: *"Why does my Kubernetes deployment keep failing when autoscaling triggers?"*

Multi-tenant RAG breaches occur when vector retrieval engines optimize strictly for semantic relevance over tenant isolation, causing cross-tenant data leakage and downstream observability exposure without any traditional authentication failure or database permission bypass.

**Why Sensitive Information Disclosure Is Dangerous:** Disclosure often appears to be normal system behaviour. The AI still answers confidently. The system does not crash. No error is thrown. Confidentiality failures in LLM systems are subtle because:

- Vector stores retain embedded documents until they are explicitly removed
- Embeddings approximate semantic relationships by positioning similar text close together in a geometric vector space
- Logging systems often have broader access than source databases
- Access control may be implemented in prompts instead of being enforced at the database layer

If retrieval is not deterministic and access-controlled, sensitive information can silently move across trust boundaries.

**Answer the questions below**

> What OWASP category covers sensitive data exposure in LLM systems?
> 

LLM02

#### **Task 3 - Common Disclosure Scenarios**

Sensitive information disclosure rarely begins with a dramatic exploit. It begins with normal system behaviour:

- The retrieval layer queries documents
- A prompt is constructed
- An answer is generated

The problem is not that the model is malicious. The problem is that retrieval and logging layers treat data as trusted before verifying authorisation. In this task, we examine how disclosure occurs in practice, not through hacking, but through architectural assumptions.

**Scenario: The Multi-Tenant Support Platform**

![The multi-tenant support platform](https://tryhackme-images.s3.amazonaws.com/user-uploads/69660b039266ef51dff2a92b/room-content/69660b039266ef51dff2a92b-1774269258334.png)

*A technology company deploys an internal AI assistant to help engineers troubleshoot production incidents. The assistant uses RAG. When a user submits a query, the system:*

1. *Retrieves the top-k most relevant internal documents*
2. *Constructs an augmented prompt (User Query + Retrieved Chunks + System Prompt)*
3. *Sends the prompt to the LLM*
4. *Logs the full request for debugging and quality monitoring*

*To diagnose hallucinations, the engineering team enables verbose logging in production. Every request now records:*

- *The user's query*
- *All retrieved document chunks*
- *The full augmented prompt*
- *The model's final response*

Secondary logging exposure occurs when telemetry platforms store augmented LLM prompts in plaintext, silently granting unprivileged operators, support staff, and contractors backchannel access to confidential retrieved documents that they were explicitly restricted from reading at the source database layer.

**What Actually Happened:** In a RAG system, the augmented prompt contains:

- User input
- Retrieved sensitive documents (determined by the top-k retrieval setting)
- System-level instructions

If that prompt is logged without redaction, the log becomes a secondary data store. Often, logging systems have:

- Broader access controls
- Longer retention policies
- Less granular permissions

The result is a data boundary failure through observability. The model did not leak the secret in its answer. The infrastructure leaked it in its logs.

**Case Study 1: EchoLeak (CVE2025-32711):** The Proof Pudding attack proves that proprietary model logic can be extracted without direct plaintext data leaks by systematically probing fine-grained output scores, confidence metrics, or latency side-channels to reconstruct a surrogate model.

**Case Study 2: Model Extraction via Output Probing (CVE2019-20634 – "Proof Pudding"):** The Proof Pudding attack proves that proprietary model logic can be extracted without direct plaintext data leaks by systematically probing fine-grained output scores, confidence metrics, or latency side-channels to reconstruct a surrogate model.

**Why These Scenarios Matter:** Sensitive information disclosure in LLM systems typically involves:

- Over-broad retrieval configuration
- Shared vector stores without strict segmentation
- Prompt-based access control instead of database enforcement
- Logging systems that capture full augmented prompts
- Treating embeddings as anonymised

The system may appear to function perfectly. Answers look relevant. Users are satisfied. No alerts fire. Confidentiality failures in AI systems are architectural and often silent.

**Answer the questions below**

> What mathematical mechanism determines which documents are retrieved in RAG systems?
> 

Similarity

> What retrieval parameter controls how many documents are returned?
> 

Top-k

> What CVE demonstrated zero-click prompt injection via retrieved content?
> 

EchoLeak

#### **Task 4 - Attacks on Vector Databases**

Vector embeddings approximate semantic similarity through mathematical proximity in a high-dimensional space rather than enforcing authorization policies, meaning retrieval layers automatically insert top-ranked vector matches into the prompt context window regardless of data sensitivity or access privileges.

![Embedding and Vector Disclosure explained.](https://tryhackme-images.s3.amazonaws.com/user-uploads/69660b039266ef51dff2a92b/room-content/69660b039266ef51dff2a92b-1774269301411.png)

**How Similarity Search Controls Exposure:** Similarity search measures closeness in vector space, typically using cosine similarity or Euclidean distance. The database returns the top-k most similar vectors. Documents ranked lower may never reach the model, even if they are safer.

Example (conceptual cosine similarity):

```python
import numpy as np
query = np.array([0.8, 0.1])
doc_safe = np.array([0.7, 0.2])
doc_confidential = np.array([0.79, 0.11])
def cosine(a, b):
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))
print("Safe score:", cosine(query, doc_safe))
print("Confidential score:", cosine(query, doc_confidential))
```

Output:

```python
Safe score: 0.98
Confidential score: 0.999
```

The confidential document ranks slightly higher because it is mathematically closer to the query. The system retrieves it. Not because it is authorised. Because it is closer, small changes in phrasing can shift vector positions and alter ranking order. In a high-dimensional vector space, minor phrasing changes can dramatically influence which document appears in the top-k results. Similarity determines visibility. Visibility determines influence.

**Corpus Manipulation and Retrieval Influence:** Vector databases store embeddings persistently. If documents are inserted into this space, whether maliciously or unintentionally, they can alter retrieval behaviour. If a document's embedding is geometrically close to frequently issued queries, it may repeatedly appear in top-k results. Over time, this changes what the model consistently sees at inference time. The model itself is not retrained. Its outputs change because its retrieval context changes. Here, retrieval manipulation can alter confidentiality boundaries.

**Embedding Inversion:** Embeddings are often assumed to be anonymised because they are numeric. This assumption is incorrect. Embedding inversion techniques attempt to reconstruct the approximate original text from stored vectors. If attackers gain access to a vector database, they may train surrogate models that map vectors back to text patterns. Even partial reconstruction can reveal:

- Named entities
- Project identifiers
- Numerical values
- Sensitive document themes

The absence of plaintext does not eliminate disclosure risk.

**Membership Inference:** Sometimes disclosure does not require reconstruction. It only requires confirmation. Membership inference attempts to determine whether a specific record exists in the system. By generating a candidate embedding and measuring similarity scores or system behaviour, attackers may confirm the presence of restricted data. In regulated contexts, confirming that a specific person, project, or incident exists in a dataset can itself constitute a privacy breach. Disclosure is not limited to output text. Inference is enough.

**Multi-Tenant Vector Stores:** Many organisations use a single vector database for multiple departments or customers. This reduces infrastructure cost but increases risk. Similarity search runs before generation. If metadata filtering is not applied before the search, vectors from different tenants may end up in the same retrieval set. Even if the model is instructed, "Only answer using documents belonging to User A," unauthorised vectors may already be inside the context window. The boundary was broken before the model responded.

**Case Study: Milvus Proxy Authentication Bypass (CVE-2025-64513):** CVE-2025-64513 in the Milvus Proxy allowed unauthenticated attackers to bypass authentication entirely by forging the `sourceId` HTTP header, demonstrating that compromising the vector database layer exposes stored high-dimensional embeddings and metadata to full extraction, deletion, or tampering.

**Answer the questions below**

> What mathematical metric is commonly used to measure similarity between embeddings?
> 

 Cosine

> What attack attempts to reconstruct text from stored vectors?
> 

Inversion

#### **Task 5 - RAG Retrieval Failures**

RAG retrieval mechanisms operate mathematically by selecting geometrically close vector chunks rather than evaluating security permissions, making pre-search metadata filtering and access control strict prerequisites to prevent unauthorized documents from entering the prompt context window.

![RAG retrieval failures explained.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f5ed9259575d24307292950/room-content/5f5ed9259575d24307292950-1775746222214.png)

**Over-Broad Top-k Retrieval:** Increasing top-k often improves answer quality. It provides more surrounding context and increases the likelihood that relevant material is included. However, increasing top-k also increases exposure risk. If the system retrieves 10 chunks instead of 3, it may include documents that are loosely related but sensitive. Even if the user did not explicitly request confidential data, geometric proximity in vector space may surface it. The model processes all tokens in the context window without distinguishing between those originating from authorised or unauthorised sources. The ranking decision determines visibility.

**Weak or Missing Metadata Filtering:** Metadata filtering restricts which documents are eligible for retrieval. It may include attributes such as tenant ID, department, or access level. If filtering is applied after similarity search, unauthorised documents may still be ranked and prepared for context construction. Filtering must occur before search, not after. Relying on prompt instructions such as "Only answer using authorised documents" does not enforce database-level isolation. Similarity search must respect policy constraints.

**Stale and Persistent Embeddings:** Vector databases are persistent storage systems. When source documents are deleted or updated in primary systems, their embeddings may remain retrievable unless explicitly removed. This creates stale exposure. A document that should no longer be accessible may still appear in similarity results. In regulated environments, retaining retrievable embeddings of deleted data may violate compliance requirements. Retrieval pipelines must synchronise with source-of-truth systems. Otherwise, old knowledge continues to influence outputs.

**Sample Code – Retrieval Misconfiguration Demonstration:** The following example simulates a minimal RAG retrieval pipeline. It shows how similarity ranking, top-k selection, missing metadata filtering, and stale embeddings directly affect what enters the context window.

```python
import numpy as np
documents = [
    {
        "id": "public_policy",
        "embedding": np.array([0.8, 0.1]),
        "metadata": {"access": "public"},
        "content": "Remote work policy allows flexible scheduling."
    },
    {
        "id": "confidential_payroll",
        "embedding": np.array([0.79, 0.11]),
        "metadata": {"access": "confidential"},
        "content": "Executive payroll adjustment memo Q4."
    },
    {
        "id": "deleted_old_policy",
        "embedding": np.array([0.78, 0.12]),
        "metadata": {"access": "public", "deleted": True},
        "content": "Old remote allowance rates (deprecated)."
    }
]
query_embedding = np.array([0.8, 0.1])
def cosine(a, b):
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))
def retrieve(top_k=2, apply_filter=False):
    scored = []
    for doc in documents:
        if apply_filter:
            if doc["metadata"].get("access") != "public":
                continue
            if doc["metadata"].get("deleted"):
                continue
        score = cosine(query_embedding, doc["embedding"])
        scored.append((score, doc))
    scored.sort(reverse=True, key=lambda x: x[0])
    return scored[:top_k]
```

Output:

```python
=== Scenario 1: No Filtering (Top-k=2) ===
public_policy | score=1.0 | access={'access': 'public'}
confidential_payroll | score=0.999 | access={'access': 'confidential'}
=== Scenario 2: No Filtering (Top-k=3) ===
public_policy | score=1.0 | access={'access': 'public'}
confidential_payroll | score=0.999 | access={'access': 'confidential'}
deleted_old_policy | score=0.998 | access={'access': 'public', 'deleted': True}
=== Scenario 3: With Metadata Filtering ===
public_policy | score=1.0 | access={'access': 'public'}
```

- In **Scenario 1**, the confidential payroll document appears because its **embedding is nearly identical to the query**. The system retrieves it purely based on similarity, not based on authorisation rules.
- In **Scenario 2**, increasing top-k to 3 introduces an additional document, including a deleted one. This shows how **over-broad top-k retrieval expands the exposure surface, allowing stale or unrelated data to enter the context window.**
- In **Scenario 3**, metadata filtering is applied before similarity ranking. Only **documents that meet access and deletion rules are considered eligible.** As a result, confidential and stale documents are excluded from retrieval entirely.

**Case Study – CVE2025-64513 (Milvus Proxy Authentication Bypass):** Access controls must be strictly enforced prior to similarity searches because once an authentication bypass (like the Milvus Proxy `sourceId` header forgery in CVE-2025-64513) surfaces unauthorized vector chunks into the prompt context window, the LLM processes those tokens indiscriminately without any native ability to perform secondary identity or permission checks.

**Answer the questions below**

> What retrieval configuration change increases exposure surface by expanding the number of ranked chunks?
> 

Top-k

#### **Task 6 - Access Control & Data Segmentation**

Effective RAG security requires deterministic access control enforced prior to vector similarity calculations via namespace filtering, per-tenant indices, and pre-query metadata constraints—because placing access policies inside system prompts or attempting post-similarity filtering leaves systems vulnerable to statistical override, prompt injection bypass, and silent confidentiality leaks.

![Access control & data segmentation](https://tryhackme-images.s3.amazonaws.com/user-uploads/69660b039266ef51dff2a92b/room-content/69660b039266ef51dff2a92b-1774269696336.png)

**Segmentation Patterns:** There are three ways people typically split up vector indexes for access control.

- **Per-Tenant Index:** Each tenant gets their own dedicated vector index. Search only hits that one index, so there's no chance of cross-contamination. Clean isolation, but you're paying for it. More indexes means more infrastructure and more things to keep in sync.
- **Per-Role Index:** Documents get grouped by access level: public, internal, confidential, whatever your tiers are. The query picks which index to search based on who's asking — simpler than per-tenant, but the identity layer and the index selector must agree exactly. If they drift, someone with an internal role is pulling confidential docs.
- **Metadata-Based Filtering:** Everything lives in one index. Each document gets tagged with metadata — tenant_id, department, and role. The query must include those filters before ranking occurs. This is what most teams actually ship. It's also where most teams mess it up, because nothing stops you from forgetting the filter or writing it wrong. The index won't complain. It'll just return everything.

**Single Shared Index Risk:** When multiple tenants share a single index and no metadata filtering is enforced, similarity search will happily return results across tenant boundaries. It doesn't know or care who owns what. Even if your application layer strips out unauthorised results after retrieval, the damage is already done. Those vectors influenced the ranking. They shaped what got returned and in what order. Filtering after the fact doesn't undo that. A shared index without hard filtering at query time means that unrelated tenants implicitly trust each other. There's no wall between them. Just the assumption that someone upstream will handle it. Similarity search follows math, not org charts. If you want boundaries, you have to build them into the query.

**Case Study – CVE2024-3033 (AnythingLLM VectorDB Authorisation Bypass)**

![cve-2024-3033](https://tryhackme-images.s3.amazonaws.com/user-uploads/69660b039266ef51dff2a92b/room-content/69660b039266ef51dff2a92b-1774269696390.png)

CVE-2024-3033 in AnythingLLM demonstrates that logical workspace partitioning fails completely as a security boundary when administrative vector management APIs are left unauthenticated, proving that deterministic access control and namespace isolation must be enforced at the API and retrieval gateway layer to prevent cross-workspace enumeration, unauthorized vector manipulation, and exposure.

**Answer the questions below**

> What logical grouping inside a vector database separates datasets?
> 

Namespace

> Which segmentation model provides the strongest isolation but at a higher cost?
> 

Per-tenant

> What type of enforcement operates before computation instead of after?
> 

Deterministic

#### **Task 7 - Best Practices & Safeguards: Reducing Disclosure Risk**

Transitioning from failure modes to active defensive engineering requires implementing layered zero-trust safeguards across document ingestion, metadata-scoped retrieval, and telemetry logging—ensuring that confidentiality boundaries are deterministically enforced before untrusted content ever reaches the LLM context window.

![Defense mechanisms explained.](https://tryhackme-images.s3.amazonaws.com/user-uploads/69660b039266ef51dff2a92b/room-content/69660b039266ef51dff2a92b-1774269762209.png)

**Redaction at Ingestion:** Sensitive data needs to be caught and stripped out before you generate embeddings. Once something gets embedded, its semantic meaning is baked into the vector store. You can't un-embed it without re-indexing. Redaction strategies may include:

- Removing PII fields
- Masking identifiers
- Replacing secrets with placeholder tokens
- Running Named Entity Recognition (NER) before anything gets indexed

The idea is simple: if it never enters the index, it can never get retrieved. Kill the exposure at the source. **The tradeoff is real, though. Redact too aggressively and your retrieval quality drops.** The embeddings lose the context they need to match well. It's a balancing act between safety and usefulness, and every team draws that line somewhere different.

**Retrieval Filtering (Allowlist-Based Eligibility):** Every retrieval query needs hard eligibility constraints applied before similarity ranking runs. Not after. Not in the prompt. In the query itself. This means:

- Enforcing tenant_id filters so one tenant's data never leaks into another's results
- Restricting by role or clearance level
- Excluding archived or deleted content that shouldn't be searchable anymore
- Applying all of this at the database level, not in application logic

Similarity search should only ever see documents the user is already allowed to access. The candidate set gets locked down first, then ranking happens inside that fence. **The tradeoff: the more filtering rules you stack, the more complex your query logic gets.** Someone has to build it, test it, and make sure it doesn't quietly break when roles change or new metadata fields show up.

**Logging Minimisation:** Logs are where people forget to look. Most teams spend time locking down retrieval and prompts, then dump everything into plaintext logs that anyone with server access can read. Production systems should avoid logging:

- Full augmented prompts
- Retrieved document chunks
- Raw embeddings
- Sensitive metadata

Instead, log the minimum you need to trace what happened:

- Document IDs
- Hashes
- Minimal structured event summaries

If your logs contain the same content you're trying to protect in the retrieval layer, you've built a second, unguarded copy of the data. Every field you log is a field that can leak. **The tradeoff is real. Thinner logs make debugging harder.** The answer isn't to log everything or nothing. It's to decide ahead of time exactly what you need for incident response and cut the rest.

**Data Retention Controls:** Vector databases don't forget, unless you make them. If a document is deleted from the source system but its embedding remains in the index, it's still searchable. Still retrievable. Still a liability. Deleting a document has to trigger the full chain:

- Embedding removal
- Index updates
- Cache invalidation

Miss any one of those, and the data is still live somewhere.  Retention policies also need to cover the stuff people forget about:

- Archived embeddings that nobody cleaned up
- Debug artifacts from testing
- Temporary vector stores that were supposed to be temporary

**The tradeoff is operational overhead. Cleanup jobs need to run, someone needs to verify they actually worked, and edge cases will show up.**

**Monitoring & Detection:** You can't just set up controls and walk away. Disclosure risk shifts over time, and you need to be watching for it. Signals worth monitoring:

- Unusual spikes in retrieval volume
- Cross-tenant access attempts
- Repeated similarity probing, where someone is methodically testing what comes back
- Unexpected namespace enumeration
- Retrieval of documents flagged as sensitive

On the output side, pattern-based detection can scan generated responses for things that should never appear. API keys, SSNs, internal tokens. Catch them before the response leaves the system, not after a user reports it. **The tradeoff is false positives. Flag too aggressively and legitimate queries start getting blocked or delayed.** Tuning detection thresholds is ongoing work, not a one-time setup.

**Red-Team Testing Approach vs Blue-Team Continuous Controls**

![Red team vs blue team controls](https://tryhackme-images.s3.amazonaws.com/user-uploads/69660b039266ef51dff2a92b/room-content/69660b039266ef51dff2a92b-1774269762054.png)

**Why Safeguards Must Be Layered:** No single control covers everything. Each one handles a different part of the pipeline, and the gaps between them are where disclosure happens.

- **Redaction** catches sensitive data before it enters the index.
- **Filtering** ensures retrieval only touches what the user is allowed to see.
- **Segmentation** keeps tenants from bleeding into each other.
- **Logging** discipline stops you from accidentally creating a second copy of protected data.
- **Monitoring** picks up what slips through everything else.

The risk isn't usually one big failure. It's two or three small ones interacting in ways nobody planned for. A redaction rule that missed one field, combined with a filter that didn't account for a new role, and combined with logs that recorded the full prompt. Each one is survivable alone. Together they're a disclosure. **Defence has to cover the entire pipeline because that's where the attacks occur.** Not at one layer. Across all of them.

**Answer the questions below**

> What control removes sensitive data before embedding?
> 

Redaction

> What policy ensures deleted embeddings are removed from storage?
> 

Retention

#### **Task 8 - Practical**

Meridian Health deploys an internal AI assistant backed by an RAG pipeline. All documents, both public policies and confidential records, are stored in a single shared index with no access controls. Your job is to discover how this architecture leaks sensitive data.

**Phase 1: Overly Broad Retrieval (RAG Retrieval Failures):** Ask normal employee questions and see if confidential data leaks through. **Step 1:** Start with a safe question:

- `What is the vacation policy?`

Note the response. This should return only public information. **Step 2:** Ask about salary ranges:

- `What are the salary ranges for engineering roles?`

The assistant returns confidential salary band data. A regular employee just accessed compensation data. **Step 3:** Ask about executive pay:

- `What is the CEO's compensation package?`

The assistant returns Julia Fang's full compensation. No access check occurred before retrieval.

<img width="982" height="672" alt="image" src="https://github.com/user-attachments/assets/225b1dc7-0ff7-48e8-9546-31fdc795aa2c" />


**Phase 2: Logging Exposure (Disclosure Scenarios & Best Practices with Safeguards):** Inspect what the retrieval system logged when answering your queries. **Step 4:** Request the retrieval log:

- `SHOW RETRIEVAL LOG`

The agent displays a simulated log entry showing every document chunk retrieved for your last query, along with their classification tags. Notice that `[CONFIDENTIAL]` chunks were passed to the model context and logged in full. In a production system, anyone with access to these logs can view the confidential data, even if the model's response has been filtered.

**Phase 3: Semantic Collision (Embedded and Corpus Poisoning):** Ask a question about a public topic and observe confidential data appearing due to semantic similarity. **Step 5:** Ask about employee benefits:

`Tell me about employee benefits enrollment`

The assistant should return the public benefits overview AND a confidential HR record for Employee #2201, Tom Russo (benefits enrollment flagged for a dependent eligibility audit). This leaked because the words "benefits" and "enrolment" appear in both the public benefits policy and the confidential performance review. Embeddings placed them close together in the vector space, allowing both to be retrieved. This is a semantic collision.

**Phase 4: Apply Access Control (Access Control and Data Segmentation):** Enable metadata filtering and verify that all three disclosure types are blocked. **Step 6:** Enable filtering:

`ENABLE ACCESS CONTROL`

<img width="977" height="555" alt="image 1" src="https://github.com/user-attachments/assets/c9796678-afda-4950-a1c2-08c239874076" />


**Step 7:** Re-ask the sensitive questions:

`What are the salary ranges for engineering roles?`

`Tell me about employee benefits enrollment`

`What is the CEO's compensation package?`

All should now be denied, or return only public data. The salary and CEO queries are blocked entirely. The benefits query returns only the public benefits overview without Tom Russo's record. **Step 8:** Confirm public data still works:

`What is the vacation policy?`

Returns the full vacation policy as before. Filtering restricts confidential data without breaking public access.

<img width="976" height="588" alt="image 2" src="https://github.com/user-attachments/assets/63f0dc22-c656-4e08-b445-3a1ff4a3962e" />


**Answer the questions below**

> What caused the assistant to expose confidential data?
> 

Broad Retrieval

> Why did Tom Russo's HR record appear when asking about benefits?
> 

Semantic Collision

> What control could have prevented the disclosure in Phase 2?
> 

Metadata Filtering

#### **Task 9 - Conclusion**

**Guarding the Context, Not Just the Model:** Everything in this room came back to the same point. LLM systems leak sensitive information without anyone exploiting the model itself. The model didn't choose to disclose anything. The pipeline fed it data it should never have seen, and it did what it always does. It used whatever was in the context window.

Confidentiality in LLM systems is a pipeline problem. Not a model problem. Not a prompt engineering problem. A pipeline problem. If unauthorised data makes it into the context window, the exposure has already happened. It doesn't matter what the system prompt says, what guardrails you added, or how carefully you tuned the output filters. The model saw it. You can't walk that back. Security has to be enforced before the similarity search runs. Before ranking. Before the context window gets assembled. By the time the model is generating a response, it's too late to decide what it should and shouldn't know.

**Key Takeaways:** Sensitive information disclosure in RAG systems usually comes down to a handful of recurring mistakes:

- Top-k retrieval that pulls too broadly and drags in documents from outside the user's scope
- Metadata filtering that's missing or misconfigured
- Shared vector indexes where tenants aren't properly isolated from each other
- Stale embeddings from deleted documents that are still sitting in the index, still searchable
- Logging systems that record full augmented prompts, creating an unguarded copy of the data
- Namespace enforcement that exists on paper but breaks under edge cases

Similarity search is math. It finds the closest vectors. That's all it does. It has no concept of who should see what, which department owns a document, or whether something was supposed to be deleted last month.

Authorisation is policy. And if that policy isn't enforced at the vector layer, before the search runs, similarity will happily ignore every boundary you thought you had.

**Red-Team Lessons:** When you're assessing an LLM system, forget prompt tricks for a minute. The real findings are in the architecture.

- Can you influence what gets retrieved? Widen the scope, change the filters, see what comes back that shouldn't.
- Can you trigger similarity results that cross tenant boundaries?
- Are namespaces enumerable? Can you figure out what other tenants or collections exist just by poking around?
- Are embeddings directly accessible through the API or some debug endpoint nobody locked down?
- Are logs capturing full context windows in plaintext somewhere?

The uncomfortable part about disclosure in these systems is that it's quiet. Nothing crashes. No error messages. The system looks stable, responses seem normal, and the whole time it's surfacing sensitive associations that nobody authorised. You won't find it unless you're specifically looking for it.

**Blue-Team Lessons:** Defenders need to stop thinking of vector databases as just search infrastructure. They're sensitive storage. Treat them that way.

- Enforce metadata filtering before running similarity searches. Not in the application layer. Not in the prompt. In the query.
- Apply deterministic namespace isolation to prevent tenants from accidentally seeing each other's data.
- Remove stale embeddings during retention cycles. If the source document is gone, the embedding should be gone too.
- Minimise logging exposure. If your logs contain the same content you locked down in retrieval, you've built a side door.
- Continuously monitor for abnormal retrieval behaviour: volume spikes, cross-tenant access attempts, repeated probing.

The model will not enforce any of this. It can't. It works with whatever lands in the context window and has no idea whether it should be there. The retrieval engine is the last line of defence before the model sees anything. That's where segmentation lives or dies.

**Framework Alignment:** This room focused on: **OWASP LLM02 – Sensitive Information Disclosure.** Related OWASP risks include:

- **LLM01** – Prompt Injection (when retrieval trust is abused)
- **LLM05** – Supply Chain Vulnerabilities (when external corpora are indexed)

From a governance perspective:

- The **NIST AI Risk Management Framework (AI RMF)** emphasises data governance and access control as foundational risk controls.
- The **EU AI Act** requires appropriate data management and technical safeguards for systems handling sensitive information.

LLM disclosure risk is not theoretical. It is an architectural governance issue.

**Bridge to Upcoming Challenges:** In the **next challenge rooms**, you will encounter scenarios where disclosure and poisoning intersect. You will analyse systems where attackers:

- Inject malicious documents
- Exploit retrieval boundaries
- Manipulate embeddings
- Trigger exfiltration pathways

Now that you understand the risks of LLM02 and have practiced deterministic mitigations, you are prepared to detect and defend against combined poisoning and disclosure attacks.
