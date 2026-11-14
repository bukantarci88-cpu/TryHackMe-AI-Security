# AI Security 1.3. AI Models & Data

## Task 1 - Introduction

AI security risks originate long before deployment, as early decisions regarding training data collection, web scraping, credential management, and fine-tuning fundamentally shape model behavior, vulnerabilities, and safety guardrails.

PII Personally Identifiable Information is any representation of data that can be used to identify an individual directly.

**Learning Objectives**

- Understand where AI training data  comes from and the security risks introduced by poor data provenance
- Recognise how PII and sensitive credentials can become permanently embedded in model weights through large-scale web scraping
- Understand how key model-building decisions (overfitting, quantisation, and federated learning) each introduce distinct security risks
- Understand the inheritance problem and what organisations unknowingly take on when fine-tuning pre-trained models
- Recognise why trained models are opaque black boxes, and what model cards do (and fail to do) to address this

## **Task 2 - Training Data**

**Where Does the Data Come From?**

| Source | What it is | Trust profile |
| --- | --- | --- |
| Web scraping | Automated crawls of public internet content (news, forums, blogs, social media, etc.) | Low: no curator, no version control, content changes after collection |
| Licensed datasets | Data purchased or agreed with platforms (e.g., OpenAI + Reddit, Meta's own social posts) | Medium: terms often unclear; original users rarely consented to  training use |
| Synthetic data | AI-generated content used to train further  AI systems | Variable: growing fast; ~12% of fine-tuning datasets now contain LLM-generated content |
| Internal corpora | Company knowledge bases, support transcripts, clinical notes used for fine-tuning | Higher: organisation has direct control, but also direct liability if mishandled |

Common Crawl serves as the foundational pre-training dataset for major modern Large Language Models, supplying up to 60% or more of the massive multi-trillion token corpuses used by model families like GPT, LLaMA, and DeepSeek.

**Data Provenance:** describes the ability to answer where data came from, when it was collected, and whether it has been modified.

**The Problem of Provenance**

Data provenance is the ability to answer three questions about any piece of training data:

1. Where did it come from?
2. When was it collected?
3. Has it been modified since?

The AI equivalent is the **ML-BOM**: a documented inventory of dataset sources, licenses, PII categories, and filtering decisions. Adoption is still early, and most organisations deploying third-party models today have nothing close to one.

**PII in the Pipeline**

One of the most direct consequences of undocumented, large-scale web scraping is that personally identifiable information ends up baked into model weights. Once it's there, it's very difficult to remove.

**Answer the questions below**

> What term describes the ability to answer where data came from, when it was collected, and whether it has been modified?
> 

Data Provenance

> What is the name of the most widely used public corpus that underpins essentially every major model family?
> 

Common Crawl

> What is the AI equivalent of a Software Bill of Materials (SBOM), used to document dataset sources, licenses, and filtering decisions?
> 

ML-BOM

## **Task 3 - Training Data**

**Epochs and Overfitting**

An **epoch** is one complete pass of the training  algorithm through the entire dataset. In practice, models are trained over many epochs. The algorithm repeatedly sees the same data, adjusting its parameters each time until it converges on accurate redictions.

**Overfitting** is one mechanism by which a model can "memorise" specific details from its  training data, including sensitive ones, making it more likely to reproduce them when prompted.

**Model Validation**

To catch overfitting early, a portion of the training data is held back and never used for training; this is the **validation set**. At regular intervals during training, the model is tested on unseen data to check whether its performance is actually generalising or just 
improving on the training examples

**Post-Training Optimisation: Pruning and Quantisation**

| Technique | What it does | Security consideration |
| --- | --- | --- |
| Pruning | Removes parameters that contribute little to predictions, shrinking model size | Changes model behaviour post-training; rarely documented in detail |
| **Quantisation** | Reduces numerical precision of weights (e.g., 32-bit to 8-bit floats) to cut memory and compute requirements | Can 
degrade safety-aligned behaviour; backdoor defences tested on 
full-precision models may fail to detect threats in quantised versions |

**Federated learning** flips this: the model is trained across many decentralised devices or organisations, with each participant training locally on their own data and only sending weight 
updates (not the raw data itself) back to a central server for aggregation.

**Answer the questions below**

> What term describes one complete pass of the training algorithm through the entire dataset?
> 

Epoch

> What problem occurs when a model memorises training data rather than learning general patterns?
> 

Overfitting

> What post-training optimisation technique reduces the numerical precision of model weights to cut memory and compute requirements?
> 

Quantisation

> What training approach trains a model across decentralised devices, sending only weight updates rather than raw data to a central server?
> 

Federated Learning 

## **Task 4 - The Inheritance Problem**

**Pre-Trained Models & Fine-Tuning**

Pre-trained foundational models learn general language, reasoning, and world knowledge from massive web-scale datasets, which major AI providers then distribute via open weights or APIs.

Fine-tuning further trains a pre-trained base model on a specialized, domain-specific dataset to adapt its general capabilities for targeted applications like healthcare or legal tasks.

**The Inheritance Problem**

This is where the security concern sits. When you fine-tune a pre-trained model, you inherit everything that model already contains. This includes things you cannot see and did not choose:

- Biases baked in during pre-training persist
- Unexpected behaviours introduced by the base model's training data carry through
- Any safety alignment built into the base model is not as durable as it might appear

## Inheritance Tax

Deploying a fine-tuned model carries forward all hidden security risks, data flaws, and supply chain vulnerabilities embedded within the underlying pre-trained base model.

**Answer the questions below**

> What is the process of taking a pre-trained model and continuing to train it on a smaller, task-specific dataset?
> 

Fine-tuning

> What term describes a model that has already been trained on a large general-purpose dataset?
> 

Pre-trained Model

## **Task 5 - The Black Box Problem**

Model Cards:  documentation artifact accompanies a model to describe what it is, how it was built, and where it falls short.

Weights:  the billions of floating-point numbers that make up a trained model collectively referred to.

A well-formed model card should give you the answers to the questions you can't get by inspecting the weights themselves:

| Section | What it should tell you |
| --- | --- |
| Training data | What sources were used, how they were filtered, known gaps or biases |
| Intended use | What the model was designed for (and explicitly what it wasn't) |
| Evaluation results | Performance metrics across different conditions and demographics |
| Known limitations | Conditions under which the model is known to underperform or behave unexpectedly |
| Bias assessment | Where  or evaluation may have introduced skew |
| Licence | What you're legally permitted to do with the model |

The model card is your audit trail. In the absence of one, there is no audit trail, just a black box and the hope that whoever built it was thorough. In security, hope is not a control.

**Answer the questions below**

> What documentation artifact accompanies a model to describe what it is, how it was built, and where it falls short?
> 

Model Card

> What are the billions of floating-point numbers that make up a trained model collectively referred to as?
> 

Weights

## **Task 6 - Practical**

Built to resemble **HuggingFace**, one of the most widely used platforms for hosting and sharing open-source AI models. Because anyone can upload a model, it's a powerful resource and a real supply chain risk.

 
Complete the exercise to get the flag! THM{A_m0del_Stud3nt}

<img width="938" height="820" alt="image" src="https://github.com/user-attachments/assets/8050ab06-b45c-4418-a468-e4cb2bce598f" />

<img width="1022" height="817" alt="image 1" src="https://github.com/user-attachments/assets/9e30cbf0-0784-49b6-9d0a-22ebc1008bce" />

<img width="1007" height="822" alt="image 2" src="https://github.com/user-attachments/assets/73df2815-3df9-4b0c-9b6e-ad512876a0d7" />

<img width="1026" height="833" alt="image 3" src="https://github.com/user-attachments/assets/20745544-d096-4dd6-896b-fd9916ef1a1d" />

<img width="1017" height="690" alt="image 4" src="https://github.com/user-attachments/assets/38b029f3-c6c5-497a-bc35-7c831e2c147c" />

<img width="1002" height="830" alt="image 5" src="https://github.com/user-attachments/assets/bdce0461-a264-4dcc-80ff-e165040e47f4" />

<img width="1005" height="867" alt="image 6" src="https://github.com/user-attachments/assets/846e7780-6639-4395-a662-eac6c843e84d" />


## **Task 7 - Conclusion**

Explored how the security risks of AI systems are shaped long before a model ever reaches production. From the unaudited data pipelines that feed pre-training, to the inherited vulnerabilities carried through fine-tuning, to the simple fact that no one can look inside a trained model and see what it's actually doing, the risks stack up at every stage of the build process, long before anyone thinks to ask a security question. With this foundation, you're better equipped to ask the right questions when considering adoption or deployment of : where did this model come from, what does it contain, and does anyone actually know?

Here's a summary of this room's key takeaways:

- AI training data is drawn from poorly documented, unaudited sources, meaning most organisations have no reliable answer to where their training data came from, what it contained, or whether it was tampered with
- PII and live credentialsroutinely end up baked into model weights through large-scale web scraping and cannot be patched out once the model is deployed
- Model-building decisionssuch as quantisation and federated learning introduce security trade-offs that are rarely documented, meaning organisations inheritunknown behaviour modifications alongside efficiency gains
- Fine-tuning a pre-trained model inherits everything beneath it: safety alignment erodes with as few as 10 adversarial examples, and fine-tuned models are measurably more susceptible to prompt injection than their base counterparts
- Trained model weights are fundamentally opaque; security testing can only sample behaviour rather than audit it, and model cards (the primary transparency mechanism) remain voluntary, frequently incomplete, and sometimes absent entirely
