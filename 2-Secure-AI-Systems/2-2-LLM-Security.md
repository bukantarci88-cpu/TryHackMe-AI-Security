# Al Security 2.2. LLM Security

### **Task 1 - Introduction**

The four-domain LLM attack surface categorizes vulnerabilities across data ingestion, model reasoning, system execution, and user interaction to secure the AI lifecycle against poisoning, manipulation, unauthorized actions, and data exposure.

### **Task 2 - Data-Based Threats**

LLM Data Memorization occurs when a model probabilistically absorbs sensitive text from its training corpus, allowing adversaries to extract raw PII, credentials, or proprietary source code through targeted output probing.

<u>**1. Training Data Extraction:**</u> Training Data Extraction attacks query an LLM to generate and statistically analyze outputs to recover verbatim private text, PII, or secrets directly from the model's training set.

<u>**2. Membership Inference:</u>** Membership Inference attacks test a known candidate data sample against an LLM's confidence or loss metrics to determine whether that specific sample was part of its training dataset.

<u>**3.Prompt leakage:</u>** System Prompt Leakage uses prompt injection techniques to trick an LLM into revealing its hidden developer instructions, exposing internal rules, business logic, and potential attack vectors.

Practical: Ask your chatbot assistant to give you the Task 2 demonstration. You'll then need to use a membership inference attack to determine which of the three placeholder samples is a member.

> Which sample is a member?
> 

MI_SAMPLE_ALPHA

<img width="643" height="542" alt="image" src="https://github.com/user-attachments/assets/3464786d-dec4-4a51-a757-8e6e1c3b7f3b" />


> Which attack determines whether a known data sample was part of an LLM’s training set?
> 

Membership inference

> Which data-based threat involves the model reproducing memorised snippets of its training data?
> 

Training data extraction

### **Task 3 - Model-Based Threats**

Model-based threats target the LLM's internal parameters and representations to steal intellectual property or extract sensitive, memorized training data through output probing and inversion.

Model Extraction attacks use large volumes of chosen API queries to collect input-output pairs and train a surrogate model, effectively stealing the target model's functionality, decision boundaries, and intellectual property.

Model Inversion attacks iteratively query a model or decode its outputs to reconstruct unknown, sensitive training data embedded within its internal representations, leading to severe privacy breaches.

Practical: Ask your chatbot assistant to give you the Task 3 demonstration. You'll then need to reconstruct this known redacted piece of training data:

> What is the employee ID?
> 

7814

<img width="636" height="360" alt="image 1" src="https://github.com/user-attachments/assets/76e5447b-2073-4108-a766-acfa60549209" />


> Which model-based threat attempts to reconstruct sensitive information encoded within a model’s internal representations?
> 

Model inversion

### **Task 4 - System-Based Threats**

System-integration vulnerabilities stem from processing trusted instructions and untrusted inputs in a single context window without native boundaries, enabling prompt injection, resource abuse, and memory poisoning.

<u>**Prompt Injection:**</u> Prompt Injection exploits the uniform processing of an LLM's context window by embedding malicious text in user inputs or retrieved data to subvert trusted system instructions and trigger unauthorized behavior.

**Context Overflow (LLM10:2025 — Unbounded Consumption)**

**Context Overflow:** Context Overflow attacks flood the LLM with excessive tokens to force the early eviction of critical system instructions and safety rules from the context window, causing safeguard bypass, response degradation, or financial Denial-of-Wallet (DoW) resource exhaustion.

**Memory Poisoning:** Memory Poisoning attacks iteratively inject malicious or deceptive statements into persistent dialogue history over multiple conversational turns, permanently corrupting long-term context and contaminating future model responses.

> Did you convince the model? Whats the flag?
> 

THM{MEMORY_POISONED}

<img width="637" height="547" alt="image 2" src="https://github.com/user-attachments/assets/e0bb9c11-9403-493b-8590-fd5803db32c6" />


> Which system component combines system instructions, retrieved data, and user input into a single sequence?
> 

The Context Window

### **Task 5 - User-Based Threats**

LLMs act as force multipliers for human-targeted cyber threats by enabling attackers to rapidly generate highly convincing, personalized scams, phishing campaigns, and misleading content at scale.

**LLM Powered Social Engineering:** LLM-powered social engineering eliminates traditional red flags like typos and awkward phrasing, leveraging context to generate hyper-persuasive spear-phishing emails that convincingly impersonate trusted colleagues or executives to trick users into fraud or unauthorized actions.

**Trust Exploitation (LLM09:2025 — Misinformation):** Trust Exploitation occurs when users over-rely on an LLM's authoritative tone, leading them to uncritically accept fabricated hallucinations or maliciously manipulated responses as factual, unsafe, or harmful truth. 

Practical: Alright, time for the last demonstration. Ask your assistant to begin with Task 5. Your job this time is to validate the LLM's advice.

> Which package should you NOT download?
> 

robbco-llm-audit

<img width="641" height="257" alt="image 3" src="https://github.com/user-attachments/assets/bead08eb-7f67-472a-8775-aa01d9af2bfc" />

> LLM-powered social engineering primarily amplifies which existing attack category?
> 

Phishing

### **Task 6 - Conclusion**

| **Type** | **Threat** | **Target / Attack Surface** | **Input** | **Output** |
| --- | --- | --- | --- | --- |
| **Data-Based** | **Training Data Extraction** | Training dataset (confidentiality) | Crafted prompts designed to trigger memorised content | Verbatim or near-verbatim training data (text, PII, secrets) |
| **Data-Based** | **Membership Inference** | Training dataset membership (privacy metadata) | Known candidate data sample already possessed by the attacker | Yes/no (or probability) decision indicating whether the sample was used in training |
| **Data-Based** | **Prompt Leakage / System Prompt Exposure (LLM07:2025)** | System prompt / developer instructions | Prompts asking the model to reveal or reflect on its instructions | Partial or full disclosure of hidden system or developer prompts |
| **Model-Based** | **Weight Extraction (Model Stealing)** | Model parameters (intellectual property) | Large volumes of carefully chosen API queries | A surrogate or distilled model replicating the original model's behaviour |
| **Model-Based** | **Model Inversion** | Model's internal representations | Unknown or partially known data, or model embeddings/outputs | New training data or attributes reconstructed from the model |
| **System-Based** | **Context Window Poisoning (Prompt Injection)** | LLM context window (instruction hierarchy) | Attacker-controlled text embedded in input or retrieved content | Altered behaviour, policy bypass, unintended actions |
| **System-Based** | **Context Overflow / Unbounded Consumption (LLM10:2025)** | Context window size and system resources | Excessively large prompts or documents | Truncated safeguards, degraded responses, or denial of service |
| **System-Based** | **Stateful Conversation Manipulation (Memory Poisoning)** | Persistent conversation memory | Malicious statements intended to be stored as long-term context | Persistent misinformation or corrupted future responses |
| **User-Based** | **LLM-Powered Social Engineering** | Human cognition and decision-making | Contextual or personal information used to craft persuasive output | Manipulated users (phishing success, fraud, coerced actions) |
| **User-Based** | **Trust Exploitation / Misinformation (LLM09:2025)** | User trust and judgment | Confident but incorrect or maliciously framed prompts | Users accepting false, unsafe, or harmful information |

**Key Takeaways**
1. LLMs introduce a unique attack surface distinct from traditional ML systems, driven by natural language interaction, context handling, and emergent behaviour.
2. Data-based threats exploit how LLMs learn from and memorise training data, enabling attacks such as training data extraction, membership inference, and system prompt leakage.
3. Model-based threats target the model itself, including model extraction (theft of model behaviour or weights) and model inversion (reconstructing sensitive training data).
4. System-based threats arise from how LLMs process all inputs as a single context, enabling prompt injection, context window overflow, and memory poisoning.
5. User-based threats leverage LLMs as force multipliers for social engineering, increasing the effectiveness of phishing, scams, and trust exploitation.
