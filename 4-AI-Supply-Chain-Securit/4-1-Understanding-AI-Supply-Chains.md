# Al Security 4.1. Understanding AI Supply Chains

#### **Task 1 - Introduction**

AI supply chain attacks exploit trust in public model repositories (like Hugging Face) by embedding malicious code into legitimate-looking model files, resulting in immediate arbitrary code execution or reverse shells upon loading (e.g., via `model.load()`) before inference even begins.

#### **Task 2 - What Is a Supply Chain?**

**Software Supply Chains:** Software works the same way. A modern Python application might depend on dozens or sometimes hundreds of third-party packages. When you run `pip install`, you are not just trusting the packages you listed: you are also trusting every **transitive dependency**, a package pulled in automatically by one of your dependencies rather than by you directly. This chain of trust is long, and every link is a potential point of compromise.

![Traditional vs Software Supply Chain](https://tryhackme-images.s3.amazonaws.com/user-uploads/69650d18bb3fe8c456972924/room-content/69650d18bb3fe8c456972924-1775117898319.png)

*Supply chain analogy. A bad supplier component reaches the build site, whether the materials are bricks or bytes.*

**Why Supply Chain Attacks Work: S**upply chain attacks are effective because they **exploit trust rather than bypass defences**. Instead of attacking your application directly, an attacker compromises something your application already trusts.

Consider two high-profile examples from traditional software:

| **Incident** | **Year** | **What Happened** | **Impact** |
| --- | --- | --- | --- |
| **SolarWinds** | 2020 | Attackers injected malicious code into the Orion build process. Customers installed a legitimate-looking update that contained a backdoor. | ~18,000 organisations compromised, including US government agencies |
| **Log4Shell** | 2021 | A critical vulnerability in the widely used Log4j logging library allowed remote code execution. | Affected millions of Java applications worldwide |

In both cases, the victims did nothing wrong from their own perspective. They installed trusted software from trusted sources. The compromise happened upstream, in the supply chain itself. The **key insight** here is that supply chain attacks scale efficiently. When an attacker successfully compromises one widely used component, they gain access to every system that depends on it.

**Answer the questions below**

> In the SolarWinds attack, where in the supply chain was the malicious code injected?
> 

Build Process

> While installing torch Pip also pulls in filelock, which you never listed. What type of dependency is filelock?
> 

Transitive Dependency

#### **Task 3 - The AI Supply Chain**

 The AI supply chain expands traditional software risk by introducing serialized model artifacts—where pre-trained weights and fine-tuning adapters (like LoRA) can execute arbitrary code upon loading (`model.load()`) or carry persistent backdoors that survive downstream transfer learning across thousands of dependent applications.

![Transfer Learning Risk: Inherited Backdoors](https://tryhackme-images.s3.amazonaws.com/user-uploads/69650d18bb3fe8c456972924/room-content/69650d18bb3fe8c456972924-1775119063141.png)

*Fine-tuning adapts the behaviour. It cannot remove what was baked in during pre-training.*

**The Four Components:** An AI supply chain consists of four key components. Each introduces its own trust relationships and attack surfaces.

![AI Supply Chain as Conveyor Belt](https://tryhackme-images.s3.amazonaws.com/user-uploads/69650d18bb3fe8c456972924/room-content/69650d18bb3fe8c456972924-1775147665819.png)

*Your application trusts the entire supply chain. One malicious component among thousands of legitimate ones is all it takes.*

| **Component** | **What It Is** | **Where It Comes From** | **Example** |
| --- | --- | --- | --- |
| **Models** | Pre-trained weights and architectures | Hugging Face Hub, TensorFlow Hub, PyTorch Hub | `bert-base-uncased`, `gpt2` |
| **Datasets** | Training and evaluation data | Hugging Face Datasets, Kaggle, academic repositories | ImageNet, Common Crawl |
| **Frameworks** | ML libraries that train and run models | PyPI, conda, GitHub | PyTorch, TensorFlow, scikit-learn |
| **Dependencies** | Supporting packages the frameworks rely on | PyPI, npm, conda-forge | NumPy, SciPy, Pillow, tokenizers |

**The Architecture:** Here is how these components fit together in a typical ML deployment:

![AI Supply Chain Architecture](https://tryhackme-images.s3.amazonaws.com/user-uploads/69650d18bb3fe8c456972924/room-content/69650d18bb3fe8c456972924-1775119063084.png)

*Every arrow connects a component you depend on but did not build.*

**Answer the questions below**

> What are the four key components of an AI supply chain?
> 

datasets, dependencies, frameworks, models

> What do model files contain that allows them to run code when loaded?
> 

Serialised Objects

#### **Task 4 - Two Ways to Consume AI**

Not every AI supply chain looks the same. There are two fundamentally different ways organisations consume AI models, and each creates a different risk profile.

**Paradigm 1: Downloading Model Files:** Self-hosting downloaded model files transfers all execution risk to your infrastructure, where formats like `.pkl`, `.pt`, and `.bin` pose arbitrary code execution threats at load time, `.h5` carries embedded architecture-level execution risks, and even non-executable formats (`.safetensors`, `.gguf`) remain vulnerable to weight poisoning and unverified quantization transformations.

**Paradigm 2: Calling Models via API:** Increasingly, organisations consume AI through **hosted API services** from providers such as OpenAI, Anthropic, and Google, or aggregators such as OpenRouter. You send a prompt, the provider runs inference, and you receive a response. You never touch the model file. This might seem safer (no pickle files to worry about), but you are still trusting a supply chain. It is just a **different** supply chain:

| **Supply Chain Element** | **Download Paradigm** | **API Paradigm** |
| --- | --- | --- |
| **Model weights** | You inspect/scan them | Provider controls them; you cannot inspect |
| **Training data** | Often documented in a model card | Often undisclosed or vaguely described |
| **Fine-tuning** | You control it | Provider may fine-tune without notice |
| **System prompts** | Not applicable | Templates from untrusted sources can alter behaviour |
| **Versioning** | You pin a specific file hash | Provider may update the model behind the same API endpoint |
| **Hosting** | Your infrastructure | Provider's infrastructure, shared, multi-tenant |

> **Key insight:** When you call a model through an API, you are trusting the provider's entire pipeline: their training data curation, fine-tuning decisions, hosting security, and update practices. You cannot verify any of these independently. The supply chain is still there; it is simply hidden behind an API call.
> 

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/69650d18bb3fe8c456972924/room-content/69650d18bb3fe8c456972924-1775148338111.png)

*Download paradigm: a model file (.pkl, .safetensors, .pt, .gguf) crosses your trust boundary and runs on your infrastructure.API paradigm: only a JSON response crosses your boundary, but the supply chain behind it is invisible.*

**Answer the questions below**

> What is the dominant file format for running local large language models such as LLaMA, Mistral, and Qwen?
> 

gguf

#### **Task 5 - The Four Attack Layers**

In this task, we will map out the **attack surface** for the download paradigm: the four distinct layers where attackers can strike. Understanding these layers will help you know what to look for when evaluating models, packages, and data sources. 

![The Four Attack Layers](https://tryhackme-images.s3.amazonaws.com/user-uploads/69650d18bb3fe8c456972924/room-content/69650d18bb3fe8c456972924-1775119353830.png)

*Every stop on the supply chain route is a potential interception point. A sophisticated attacker does not need to own the entire chain. Just one checkpoint.*

**Layer 1: The Model Layer**

Model layer attacks directly target the AI artifact itself, falling into three distinct technical categories: deserialization code execution (running malicious payloads at load time), architecture tampering (embedding malicious layers or hooks), and weight poisoning (subtly altering tensors to create dormant backdoors or targeted triggers).  Three distinct levels have been identified, and each behaves differently:

- **Serialisation-level:** Code hidden inside the file format itself, and executes when the file is loaded
- **Architecture-level:** Malicious logic embedded in the model's layers, executed on every prediction
- **Weights-level:** Learned values subtly altered to misbehave on specific inputs

> **Key insight:** Stripping executable code from a model file eliminates serialisation-level attacks but leaves architecture-level and weights-level attacks completely untouched. Effective defence requires inspection at every level. The [Securing the AI Supply Chain](https://tryhackme.com/room/securing-the-ai-supplychain) room covers the tools for each of them.
> 

**Layer 2: The Dependency Layer**

This layer covers the packages and libraries your ML project depends on. Attackers exploit it through dependency confusion, typosquatting, and uploading malicious packages with professional-looking descriptions that hide malware.

**Layer 3: The Data Layer**

Training data is the foundation of every ML model, and poisoning it is difficult to detect. [Researchers have demonstrated](https://openreview.net/forum?id=eiqrnVaeIw) that replacing as little as 0.1% of a training dataset with crafted samples can introduce a reliable backdoor without measurably affecting accuracy on clean data. For denial-of-service objectives, the effective threshold drops as low as 0.001%.

> **Note:** Data poisoning attacks are covered in depth in the [Data Poisoning](https://tryhackme.com/module/data-poisoning) module. This room focuses on the supply chain context: how poisoned data reaches your pipeline through untrusted sources.
> 

**Layer 4: The Infrastructure Layer**

The infrastructure layer covers the systems that host, distribute, and manage AI artefacts. Attackers gain access to model or package repositories, inject malicious steps into CI/CD build pipelines, or steal maintainer credentials to push malicious updates under trusted identities.

**The Compounding Effect:** These layers do not exist in isolation. A sophisticated attacker can combine techniques across layers. For instance, an attacker could:

- **Compromise an account** on Hugging Face (infrastructure layer)
- **Upload a backdoored model** with malicious pickle code (model layer)
- **Include a requirements.txt** referencing typosquatted packages (dependency layer)
- **Distribute a poisoned dataset** alongside the model (data layer)

A single download can trigger compromises at every layer simultaneously.

**Answer the questions below**

> At which layer of the AI supply chain do pickle-based attacks occur?
> 

Model Layer

> Which level of model attack is eliminated by converting to SafeTensors format?
> 

**Serialisation-level**

> Researchers find that 0.1% of a public training dataset has been replaced with crafted samples designed to introduce a backdoor. Which attack layer does this represent?
> 

Data Layer

#### **Task 6 - Real-World Incidents**

The attack techniques from the previous task are not theoretical; they have all been used in real attacks. In this task, we will examine five significant incidents from 2022 to 2025. Each one exploited a different part of the supply chain, and together they show the full range of risks you need to defend against.

> **The deception at the heart of supply chain attacks:** A model that looks trustworthy on the surface can hide malicious code inside. Don't judge a model by its star rating.
> 

![Timeline of Real Incidents](https://tryhackme-images.s3.amazonaws.com/user-uploads/69650d18bb3fe8c456972924/room-content/69650d18bb3fe8c456972924-1775147487699.png)

**Incident 1: PyTorch Dependency Confusion (December 2022):** The December 2022 PyTorch `torchtriton` breach demonstrated how traditional dependency confusion can cripple AI infrastructure, where Pip's default index precedence fetched a malicious public PyPI package over an internal dependency to silently exfiltrate SSH keys, environment files, and system credentials via encrypted DNS.

**Incident 2: Hugging Face Hub Vulnerabilities (2023–2024):** The Hugging Face Hub security incidents demonstrated a dual-layer AI supply chain threat: credential exposure enabling unauthorized repository hijacking, paired with malicious model serialization formats (`.pkl`) that disguise arbitrary execution payloads inside functional, highly rated machine learning models.

**Incident 3: Ultralytics Build Pipeline Compromise (December 2024):** Attackers compromised the GitHub Actions workflow and PyPI release credentials for the widely used Ultralytics YOLO library, embedding a cryptominer directly into official `pip` packages to execute code during normal installation.

**Incident 4: @solana/web3.js Compromise (December 2024):** Attackers hijacked a maintainer's npm account to publish malicious versions of a high-traffic library, demonstrating how stolen credentials in trusted upstream software dependencies can instantly bypass consumer security controls to exfiltrate private data.

**Incident 5: NullifAI Scanner Evasion on Hugging Face (February 2025):** ReversingLabs discovered that deliberately malformed pickle structures could bypass Hugging Face's automated Picklescan security filters while remaining fully executable upon deserialization by downstream machine learning workflows.

**Answer the questions below**

> The torchtriton package exploited pip's version resolution to install a public package over an internal one. Which of the four attack layers does this target?
> 

Dependency Layer

> The @solana/web3.js attacker stole a maintainer's credentials to push malicious updates to a legitimate, high-trust repository. Which attack layer does this represent?
> 

Infrastructure Layer

#### **Task 7 - Practical**

Now it is your turn to practise evaluating models the way you will encounter them in real life: **on repository pages**. Click the **View Site** button below. You will see a realistic Hugging Face model page, a similar interface to what you would use when browsing for models.

**Your scenario:** You are a developer at a startup. Your team lead found a sentiment analysis model (`trustworthy-ai-models/bert-sentiment-classifier`) and wants to deploy it to production. Before you run `pip install` and `model.load()`, you need to evaluate whether it is safe to do so.

The page opens on the **Model Under Review**: the one your team lead wants to use. Explore it as you would any model repository:

1. **Model Card tab**: Read the documentation. Note whether it is thorough or sparse.
2. **Files tab**: Examine the model weight format. Check for any warnings.
3. **Security tab**: Hugging Face now shows automated security scan results. Review what the scanner found.
4. **Sidebar**: Check the organisation, download count, and creation date.
5. **Community tab**: Check for discussions or warnings from other users.

Then click **"Compare: Verified Model"** to see what a legitimate, trustworthy model looks like. This is `google-bert/bert-base-uncased`, one of the most widely used models on Hugging Face, from a verified organisation with a 6+ year track record.

**Compare these signals between the two models:**

| **Signal** | **Suspicious Model** | **Legitimate Model (baseline)** |
| --- | --- | --- |
| **Organisation** | Verified? When did this account join? | Verified, established track record |
| **Downloads** | How many last month? | Millions of downloads |
| **File format** | Pickle or SafeTensors? | SafeTensors |
| **Security scan** | Any issues flagged? | No issues found |
| **Model card** | Thorough or sparse? | Thorough, with training data and limitations documented |

After exploring both, answer the questions below. Then ask yourself: **would you approve the first model for production?**

**Answer the questions below**

> In the static site, what is the name of the unverified organisation that uploaded the model?
> 

trustworthy-ai-models

<img width="760" height="536" alt="image" src="https://github.com/user-attachments/assets/83813027-ba01-4736-9251-bf379412e380" />


> How many downloads does this model have (last month)? 127
> 

<img width="550" height="532" alt="image 1" src="https://github.com/user-attachments/assets/7a939969-b1c2-4134-8f0b-56470ca2a4ae" />


> What file format does the verified model (google-bert/bert-base-uncased) use for its weights?
> 

SafeTensors

<img width="762" height="632" alt="image 2" src="https://github.com/user-attachments/assets/3cfbeff3-e220-4c92-989d-a4c6f86c2f27" />


**Task 8 - Conclusion**

The attacks in this room share a common thread. The `torchtriton` package stole keys from developers who ran a routine installation command. The malicious models on Hugging Face performed their advertised ML task correctly while silently connecting to servers controlled by attackers. The Solana package compromise lasted only hours but caused six-figure losses. **None of these attacks required breaking into anything**. In each case, the victim trusted a component that had earned that trust, and that trust was the vulnerability.

That is what makes supply chain attacks particularly hard to defend against. The malicious models JFrog found on Hugging Face were not obviously wrong. They had professional model cards, plausible organisation names, and real functionality (they performed their advertised task correctly). They passed every informal check an ML engineer would reasonably run. The compromise was invisible until anomalous network connections surfaced, sometimes weeks later.

**Key takeaways from this room:**

- **Model files are code in disguise.** Pickle-based models execute at load time; format choice determines your exposure.
- **The four attack layers are distinct.** Model, dependency, data, and infrastructure attacks require different defences and produce different forensic signals.
- **Transitive dependencies silently expand your attack surface.** You are responsible for every package pip installs, not just the ones you listed.
- **Automated scanners are not a complete defence.** The NullifAI case shows that malformed files can pass platform checks while remaining executable.
- **Infrastructure compromise beats content filtering.** Stolen credentials on a trusted repository push malicious code with full legitimacy; no tooling flags it at download time.
- **SafeTensors eliminates serialisation-level attacks.** It does not eliminate architecture-level or weight-level attacks; the format is a single control, not a complete solution.
