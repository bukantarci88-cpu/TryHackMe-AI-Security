# Al Security 2.3. AI Threat Modelling

### **Task 1: Introduction**

AI systems are already deeply embedded in critical enterprise operations, but their non-deterministic nature and unique failure modes—such as data poisoning, weight theft, and prompt injection—create novel attack surfaces that traditional frameworks like STRIDE were not built to model.

### **Task 2: AI-Specific Assets and Attack Surfaces**

Threat modeling AI systems requires expanding traditional asset inventories beyond databases and credentials to include novel assets like training corpora, model weights, prompts, and vector embeddings, which creates blind spots attackers can exploit if omitted from security assessments.

<img width="572" height="345" alt="image" src="https://github.com/user-attachments/assets/f1681004-d7c3-4cf7-a78d-eb82b6a82906" />


**AI Assets You Need to Know**

| Asset | What It Is | Why It Matters |
| --- | --- | --- |
| Training Data | The datasets used to teach the model its behaviour | Poisoning this data corrupts the model's outputs at the source. 
Unlike a database compromise, the damage is baked into the model itself. |
| **Model Weights / Parameters** | The numerical values that define what the model has learned | These *are* the model. Stealing them means an attacker has a functional copy of your AI , months of compute and potentially millions in investment, gone. |
| **Embedding Vectors** | Numerical representations of text or data used for similarity computation, retrieval, or as input features to downstream models | Used in RAG pipeline, recommendation engines, and fraud detection systems. 
Poisoning or manipulating embeddings alters what information models see 
at query time. |
| **System Prompts** | Instructions that define the model's behaviour, constraints, and persona | Leaking these reveals your security controls, business logic, and guardrails, giving attackers a roadmap to bypass them. |
| **Feature Stores** | Preprocessed data repositories that feed real-time model inputs | Tampering with features changes what the model "sees" at inference time, without touching the model itself. |
| **Model Registry / Artifacts** | Stored versions of trained models ready for deployment | A compromised registry means an attacker can swap a legitimate model
 for a backdoored one, and no one may notice until it's too late. |

Beyond new asset types, AI systems also behave differently from traditional software, affecting how we model threats. Two characteristics worth noting:

- **Non-deterministic behaviour:** AI models, especially LLMs, can produce different outputs for the same input. This makes testing, auditing, and incident reproduction significantly harder than
with deterministic software. If you've completed earlier rooms in this path, you'll already be familiar with this concept.
- **The black box problem:** Most AI models, particularly deep neural networks, lack the explainability of traditional application logic. You can't step through a model's reasoning the way you'd trace a code path. This forces defenders to think in terms of input-output
behaviour and failure modes rather than code-level inspection.

> In a RAG-based system, which AI asset type is used to retrieve relevant context at query time?
> 

Embedding Vectors

> An attacker gains access to MegaCorp's model registry and swaps the production model for a modified version. Which AI-specific asset has been compromised?
> 

Model Registry / Artifacts

### **Task 3: Data Supply Chain and STRIDE’s Gaps**

The AI Data Supply Chain: AI systems inherit traditional software supply chain vulnerabilities while introducing a distinct, unscrubbed data supply chain where compromised datasets, corrupted sources, and malicious data dependencies create novel security risks.

<img width="1147" height="547" alt="image 1" src="https://github.com/user-attachments/assets/8e7ec268-0a59-4d40-bdbb-876d74c21a6b" />


Stage 1: Data Collection: An attacker gains an initial foothold by contributing to or altering any of the diverse ingestion sources—such as web scraping, third-party feeds, or user-generated content—used to gather raw training data.

Stage 2: Cleaning and Labelling: Compromising human annotators, automated labeling tools, or feedback loops corrupts dataset labels, quietly training the model to learn flawed associations without triggering traditional data corruption alerts.

Stage 3: Model Training: Poisoned data that survives preprocessing becomes permanently embedded into the model's weight matrices over long training cycles, requiring a costly and time-consuming full retrain to remediate.

Stage 4: Validation and Packaging: Registry tampering allows attackers to swap a clean model for a backdoored variant, which easily passes standard validation suites because malicious trigger patterns are absent from test datasets.

Stage 5: Inference: Production deployments introduce runtime attack surfaces—particularly within Retrieval-Augmented Generation (RAG) and vector database pipelines—that allow dynamic context injection attacks during model execution.

**Why STRIDE Alone Falls Short**

While traditional STRIDE remains effective for standard applications, it falls short for AI systems because it cannot adequately categorize delayed and subtle training data poisoning, non-deterministic adversarial input manipulation, expanded agentic tool-use privilege escalation, or full model weight extraction.

<img width="1147" height="612" alt="image 2" src="https://github.com/user-attachments/assets/815f35ef-9867-4877-a213-b92c68bf9d75" />


> An attacker injects crafted data points into a training pipeline over several months, gradually shifting the model’s decision boundaries. At which supply chain stage does the attacker inject the malicious data?
> 

Data collection

> Which STRIDE category is insufficient for capturing the delayed, diffuse effects of training data poisoning?
> 

Tampering

### **Task 4: Adapting STRIDE for AI Systems**

**STRIDE**  **Refresher**

<img width="1215" height="274" alt="image 3" src="https://github.com/user-attachments/assets/899c2798-00a9-4324-9be0-4adec6b1ebd4" />


| Threat Category | Security Property Violated | Traditional Meaning |
| --- | --- | --- |
| **S** — Spoofing | Authenticity | Pretending to be someone or something you're not |
| **T** — Tampering | Integrity | Modifying data or code without authorisation |
| **R** — Repudiation | Non-repudiability | Denying that you performed an action |
| **I** — Information Disclosure | Confidentiality | Exposing information to unauthorised parties |
| **D** — Denial of Service | Availability | Making a system or resource unavailable |
| **E** — Elevation of Privilege | Authorisation | Gaining access or capabilities beyond what's permitted |
1. S — Spoofing: Data Source Impersonation

Spoofing in AI systems involves injecting malicious data into external retrieval sources (such as RAG vector stores) or impersonating model endpoints and identity systems to ground AI reasoning and decisions in attacker-controlled information.

2. T — Tampering: Data Poisoning

Tampering in AI targets training pipelines, registry weights, prompts, and input features to corrupt model representations and manipulate inference outputs—causing subtle, delayed, or backdoor-triggered decision failures.

1. R — Repudiation: Unexplainable Model Decisions

Repudiation in AI arises from a lack of decision auditability, where non-deterministic model outputs, black-box reasoning, and volatile retrieval contexts prevent tracing, reproducing, or attributing specific AI actions and outputs to an audited model state.

4. I — Information Disclosure: Model Extraction

Information Disclosure in AI manifests as the unauthorized exfiltration of intellectual property and sensitive data, where adversaries query public endpoints to extract model weights, invert embeddings, leak system prompts, or force the regurgitation of memorized training PII.

5. D — Denial of Service: Inference Cost Exploitation

Denial of Service in AI exploits the high computational cost of model inference, using long prompts, complex inputs, or junk training data to saturate GPU capacity and inflict financial damage through Denial-of-Wallet (DoW) token depletion.

6. E — Elevation of Privilege: Jailbreaking and Excessive Agency

Elevation of Privilege in AI occurs when attackers craft inputs that bypass model guardrails or exploit overly permissive tool integrations, effectively elevating their access to restricted capabilities, internal systems, external APIs, and unauthorized execution environments.

**STRIDE Consolidated Mapping:** 

| STRIDE Category | Primary AI Manifestation | Other AI Threats | MegaCorp Example |
| --- | --- | --- | --- |
| **Spoofing** | Data source impersonation (RAG injection) | Model impersonation, adversarial identity attacks | Fake policy docs injected into chatbot knowledge base |
| **Tampering** | Data Poisoning  | Model manipulation, prompt injection, feature tampering | Crafted transactions shift fraud model's decision boundaries |
| **Repudiation** | Lack of decision audit trails | Context volatility, model version ambiguity | Can't explain why fraud model approved a suspicious transaction |
| **Info Disclosure** | Model extraction / stealing | Training data extraction, prompt leakage, embedding inversion | Competitor reconstructs recommendation engine via API queries |
| **Denial of Service** | Inference cost exploitation (denial of wallet) | GPU exhaustion, sponge examples, pipeline disruption | Chatbot API flooded with expensive prompts; bill ($) spikes 12x |
| **Elevation of Privilege** | Jailbreaking / guardrail bypass | Excessive agency, tool exploitation, cross-plugin escalation | Jailbroken chatbot used to query customer PII via database tools |

> What is the primary AI-specific manifestation of Information Disclosure in the STRIDE-AI mapping?
> 

Model Extraction

> An attacker crafts prompts that cause an LLM to bypass its safety guidelines and content restrictions. Which STRIDE category does this map to?
> 

Elevation of Privilege

> Which OWASP LLM Top 10 (2025) entry addresses the risks of AI systems being granted too many permissions or too much autonomy?
> 

LLM06:2025 — Excessive Agency

> An attacker drives your monthly inference bill from $15,000 to $180,000 without taking your service offline. What is this type of attack commonly called?
> 

Denial of Wallet

### Task 5: MITRE ATLAS: The AI Threat Technique Catalogue

MITRE ATLAS serves as the AI-focused counterpart to traditional MITRE ATT&CK, offering a structured, matrix-based knowledge base to categorize and map adversary tactics, techniques, and real-world threat behaviors specifically targeting artificial intelligence and machine learning systems.

<img width="1319" height="574" alt="image 4" src="https://github.com/user-attachments/assets/f7252965-2d36-458b-afde-9d39da913739" />


**How Is Structured:**

ATLAS follows the same hierarchy you already know from **MITRE ATT&CK**:

| Component | What It Answers | Example |
| --- | --- | --- |
| **Tactic** | *Why* the adversary's goal | ML Attack Staging (AML.TA0012) |
| **Technique** | *How* the method used to achieve it | DATA Poisoning (AML.T0020) |
| **Sub-technique** | *Specifically how* a variant of the method | Craft Adversarial Data (AML.T0043.004) |
| **Mitigation** | *What stops it* the defensive countermeasure | Input validation, data provenance tracking |

Tactics are the columns of the ATLAS matrix. Techniques sit within those columns. When you are threat modeling, you start with a tactic (what the attacker wants to achieve) and drill into techniques (how they'd achieve it against your specific system).

<img width="906" height="588" alt="image 5" src="https://github.com/user-attachments/assets/3ae1de66-36b1-4fa0-8d7a-4f73f11a155e" />


**Key Techniques You Need to Know:** 

Data Poisoning (AML.T0020): Injecting malicious data into training pipelines to corrupt model behaviour. Effects are delayed and persist until the model is retrained on clean data. Maps to STRIDE: Tampering

Model Extraction (AML.T0024): Systematically querying a model's API to reconstruct a functional copy. Requires no internal access, just the public endpoint and enough queries. Maps to STRIDE: Information Disclosure.

Evade ML  Model (AML.T0015): Crafting adversarial data that prevents a model from correctly identifying the contents of the input. This threat spans multiple STRIDE categories simultaneously, Tampering, Spoofing, and Elevation of Privilege, depending on context. Adversaries may use this to evade malware detection, bypass content filters, or cause misclassification in downstream tasks.

LLM Prompt Injection (AML.T0051): Manipulating an LLM's behaviour by injecting instructions through direct user input or indirect content the model processes. The distinction matters: direct injection is a user crafting malicious input in the chat interface, while indirect injection is malicious instructions embedded in content the model retrieves or processes (such as documents in a RAG pipeline). For MegaCorp, indirect injection via RAG the knowledge base is the primary vector. Maps to STRIDE: Tampering

Backdoor ML Model (AML.T0018): Embedding hidden triggers in a model during training. The model performs normally on standard inputs but behaves maliciously when a specific trigger pattern is present. Think of it as a logic bomb, but inside a neural network.

Using ATLAS During Threat Modeling: 

ATLAS isn't a replacement for STRIDE, it's the enrichment layer. Here's how the two work together in practice:

- **Start with** STRIDE**:** Walk each AI component through the six threat categories to identify "*what could go wrong"*
- **Enrich with ATLAS:** For each identified threat, look up the corresponding ATLAS technique to get the specific *how*, including documented attack methods and real-world case studies
- **Apply mitigations:** ATLAS provides recommended countermeasures for each technique, giving you actionable defensive guidance

<img width="906" height="443" alt="image 6" src="https://github.com/user-attachments/assets/b821f0f9-8d82-4886-8f99-407d866160d6" />

**Real-World Case Studies**

Atlas includes 52 documented case studies (opens in new tab) of real AI attacks. Two worth noting:

ShadowRay (opens in new tab) (AML.CS0023): Attackers exploited vulnerabilities in Ray, a popular framework for distributed AI workloads, to compromise AI training infrastructure in the wild. This demonstrated that AI supply chain attacks aren't theoretical, they're happening against production systems.

Morris II Worm (opens in new tab) (AML.CS0024): Researchers demonstrated a self-replicating prompt injection worm that could spread between AI agents through RAG-based email systems. The worm injected its payload into the model's context without user interaction, extracted PII, and automatically propagated it to other agents.

Both cases are documented in ATLAS with full technique mappings, giving you a concrete reference for what these attacks look like in practice.

> What does the acronym ATLAS stand for?
> 

Adversarial Threat Landscape for Artificial-Intelligence Systems

> Which ATLAS case study described a self-replicating prompt injection worm that spread between AI agents via RAG email systems?
> 

Morris II 

> What is the ATLAS technique ID for Model Extraction?
> 

AML.T0024

### Task 6: OWASP LLM Top 10: Mapping Risks to Components

**The 2025 List With Component Mapping**

| # | Risk | What It Means | Where It Lives (Vulnerable Components) |
| --- | --- | --- | --- |
| **LLM01** | Prompt Injection | Attacker manipulates model behaviour through crafted inputs, direct or indirect | **LLM inference endpoint** (direct injection via user input), **vector database / RAG pipeline** (indirect injection via retrieved content), **any component that feeds text to the model** |
| **LLM02** | Sensitive Information Disclosure | Model outputs reveal PII, credentials, or proprietary data | **LLM inference endpoint** (model memorisation), **training pipeline** (sensitive data in training set), **system prompt** (credentials or logic embedded in prompt) |
| **LLM03** | Supply Chain | Compromised models, training data , plugins, or dependencies | **Training pipeline** (third-party datasets, compromised base models, poisoned fine-tuning data), model registry (models retrieved from external repos such as Hugging Face), **plugin/tool integrations** (vulnerable or compromised third-party dependencies) |
| **LLM04** | Data and Model Poising  | Corrupted training data or model weights alter behaviour | **Training pipeline** (data injection point), model registry (model swap), **feature store** (manipulated input features) |
| **LLM05** | Improper Output Handling | LLM outputs aren't validated before downstream use | **Web frontend** (unsanitised output rendered in browser, XSS risk), API **gateway** (model output passed to downstream services without validation), **any system consuming model responses** |
| **LLM06** | Excessive Agency | LLM granted too many permissions, tools, or autonomy | **LLM inference endpoint** (where jailbreaking enables tool abuse), **tool integrations** (database query tools, code execution, email sending), API  **gateway** (overly broad API permissions granted to the model), agentic orchestration layer |
| **LLM07** | System Prompt Leakage | Internal prompts containing sensitive logic or credentials are exposed | **LLM inference endpoint** (prompt extraction attacks), **system prompt configuration** (credentials or API keys stored in prompts rather than secure vaults) |
| **LLM08** | Vector and Embedding Weaknesses | Vulnerabilities in RAG systems, vector databases, and embeddings | **Vector database** (embedding poisoning, similarity attacks, unauthorised access), RAG **pipeline** (retrieval manipulation), embedding **generation process** |
| **LLM09** | Misinformation | LLM generates credible-sounding but false content | **LLM inference endpoint** (hallucination), **vector database** (stale or incorrect source documents), **any user-facing output channel** |
| **LLM10** | Unbounded Consumption | Uncontrolled resource usage leading to DoS or financial exploitation | **LLM inference endpoint** (expensive queries, denial of wallet), API **gateway** (insufficient rate limiting), **training pipeline** (resource-heavy junk data injection) |

Connecting OWASP Back to STRIDE and ATLAS

OWASP, STRIDE and ATLAS aren't competing frameworks, they are layers of the same assessment:

| Layer | What It Does | When You Use It |
| --- | --- | --- |
| **STRIDE-AI** | Categorises threats by type | Initial threat identification, "what could go wrong" |
| MITRE ATLAS | Documents specific attack techniques | Enrichment, "how exactly would an attacker do this" |
| **OWAPS LLM Top 10** | Maps risks to components and prioritises | Assessment and scoping, "where does this risk live and how critical is it" |

STRIDE gives you the wide-angle view. ATLAS gives you the technical detail. OWASP tells you **where to point the camera**.

> How many of the OWASP LLM Top 10 entries affect the LLM Inference Endpoint?
> 

6

> An organisation notices their chatbot is rendering LLM output directly in the browser without sanitisation. Which OWASP entry does this fall under?
> 

Improper Output Handling

> Which component in a typical LLM architecture is the primary one that needs hardening against data and model supply chain risks (LLM03)?
> 

Training Pipeline

### Task 7: OWASP LLM Top 10: Mapping Risks to Components

You'll be selecting OWASP LLM Top 10 vulnerabilities, mapping them to architecture components, and justifying your choices to put your threat modelling instincts to the test. This task can be used to practice your knowledge of AI systems and threats.

<img width="811" height="451" alt="image 7" src="https://github.com/user-attachments/assets/810976f0-aceb-4315-99ed-13ee771e231b" />

<img width="811" height="436" alt="image 8" src="https://github.com/user-attachments/assets/1d70339f-5c53-4200-8bdf-9247c4f1aace" />

<img width="791" height="448" alt="image 9" src="https://github.com/user-attachments/assets/52baf95d-805c-480e-b04e-65181eb2a2c3" />

<img width="798" height="444" alt="image 10" src="https://github.com/user-attachments/assets/3424f3fe-dd05-4207-bf7b-6327f9bb4b99" />

<img width="795" height="448" alt="image 11" src="https://github.com/user-attachments/assets/43490085-4cd8-482a-9306-de62e39120e9" />

<img width="795" height="447" alt="image 12" src="https://github.com/user-attachments/assets/a90b35ff-d44e-4ab7-b045-76cddf2a213c" />

<img width="794" height="447" alt="image 13" src="https://github.com/user-attachments/assets/971265a8-2635-46e5-acf6-34574eb0599c" />

<img width="797" height="443" alt="image 14" src="https://github.com/user-attachments/assets/72996b5d-dc1c-4e31-a81d-7799a7b0e33f" />

THM{AI_THREAT_MODEL_COMPLETE}

### Task 8: Conlusion

- **Identified AI-specific assets** training data, model weights, embeddings, system prompts, feature stores, and model registries, that expand the attack surface beyond traditional applications
- **Mapped the AI data supply chain** understanding how data flows from collection through training to inference, and where each stage is vulnerable to compromise
- **Adapted STRIDE for AI systems,** applying the six familiar threat categories with AI-specific context, from data poisoning under Tampering to jailbreaking under Elevation of Privilege
- **Enriched findings with MITRE ATLAS** using the AI-specific technique catalogue to move from general threat categories to documented attack methods with technique IDs and mitigations
- **Mapped risks to components using the OWASP LLM Top 10** the primary assessment lens that lets you look at an architecture diagram and immediately identify which components carry which risks and at what severity
- **Applied everything to MegaCorp** assessing real components, mapping OWASP risks with STRIDE and ATLAS enrichment, and generating a prioritised threat assessment

**The Workflow at a GlanceS**

<img width="906" height="481" alt="image 15" src="https://github.com/user-attachments/assets/b4e12aa4-c012-447f-82eb-8f8aa99c7295" />


This workflow is repeatable. Every time your organisation deploys a new AI system, updates a model, or introduces agentic capabilities, you can run the same process. The frameworks evolve, ATLAS adds new techniques, OWASP updates its list, but the methodology stays consistent.

**Key Takeaways:**

**AI systems aren't just traditional applications with a model bolted on.** They have different assets, a separate data supply chain, and failure modes that require adapted approaches.

**STRIDE gives you the threat categories, ATLAS gives you the techniques.** Together, they provide the vocabulary; STRIDE tells you *what type* of threat you're looking at; ATLAS tells you *exactly how an attacker* would execute it and what mitigations to apply.

**OWASP tells you where to point the camera.** The LLM Top 10 is the framework that maps risks directly to architectural components. It's what lets you look at a deployment and say *"this component carries these risks at this severity"*, and that's the skill that makes a threat assessment actionable.

- **MITRE ATLAS:** Explore the full technique catalogue, case studies, and mitigations beyond what we covered here
- **OWASP AI Exchange:** Explore broader AI security guidance including agentic AI and non-LLM systems
