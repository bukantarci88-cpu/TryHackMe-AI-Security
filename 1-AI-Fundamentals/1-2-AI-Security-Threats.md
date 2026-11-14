# AI Security 1.2. AI Security Threats

## <u>Task 1 - Introduction</u>

The world is changing; industries are coming to terms with the reality of how they will be affected by Artificial Intelligence (AI), and cyber security is no different.

## <u>Task 2 - The Building Blocks of AI</u>

**Machine Learning:** Machine Learning enables computers to learn autonomously from data through an iterative lifecycle of problem definition, data preparation, algorithm training, deployment, and continuous performance monitoring.

**Machine Learning Algorithms:** Machine Learning algorithms build models through a three-step mathematical loop, making predictions, evaluating error, and adjusting parameters, until achieving optimal performance.

**ML algorithm pieces:** Machine Learning algorithms operate across four distinct paradigms, supervised, unsupervised, semi-supervised, and reinforcement learning, to learn patterns from either labeled, unlabeled, mixed, or reward-driven data.

**Neural networks and Deep learning**
Synapses: In artificial intelligence, a synapse is the connection point between artificial neurons (nodes) in a neural network, acting as the conduit through which information flows.

<img width="613" height="511" alt="image" src="https://github.com/user-attachments/assets/d0f1d6d0-03dc-4f43-b8da-3d03d3cfd9b4" />


**ML vs DL:** Machine Learning relies on labeled data and human feature engineering, whereas Deep Learning leverages neural networks to automatically extract features from raw, unlabeled, unstructured data without human intervention.

**Answer the questions below**

> What category of machine learning combines both labelled and unlabelled data?
> 

semi-supervised learning

> What is the first layer in a neural network that handles incoming raw data?
> 

input layer

> Which learning method does not require human-labeled data and can extract features from raw, unstructured input?
> 

deep learning

> What are the weighted connections between nodes in a neural network meant to simulate in the human brain?
> 

synapses

## <u>**Task 3 - LLMs**</u>

Large Language Models leverage transformer-based self-attention and human feedback (RLHF) to sequentially predict next words, powering conversational and generative AI applications across text, image, and media creation.

<img width="391" height="491" alt="image 1" src="https://github.com/user-attachments/assets/c1fd6537-0b5c-4a5a-8673-1a10459c4a51" />


Artificial Intelligence encompasses Machine Learning, which learns patterns from data; Deep Learning, which scales ML using multi-layered neural networks; and Large Language Models, which apply transformer-based Deep Learning to master human language.

**Answer the questions below**

> What type of AI model enabled major advancements in ChatGPT and similar tools?
> 

large language models

> What is the first training stage where an LLM processes massive amounts of data?
> 

pre-training

> What type of neural network introduced by Google in 2017 powers modern LLMs?
> 

transformer

## <u>**Task 4 - AI Security Threats**</u>

**Vulnerabilities in AI Models**
AI models face unique security vulnerabilities, including prompt injection, data poisoning to skew outputs, model theft via API querying, privacy leakage of sensitive training data, and model drift over time.

**Generative AI Threats (Malware & Deepfakes)**
Generative AI accelerates cyber threats by enabling rapid automated malware generation and producing hyper-realistic DeepFakes of voice or image to bypass security controls and commit fraud.

**AI-Enhanced Phishing**
While security training has improved phishing awareness by spotting suspicious links and broken text, generative AI allows attackers to rapidly generate highly persuasive, grammatically flawless phishing emails at scale.

**Answer the questions below**

> What framework was developed by MITRE to guide the understanding of AI-specific cyber threats?
> 

ATLAS

> What type of attack involves cloning an AI model by interacting with its API?
> 

model theft

> What generative AI technique can replicate a person’s voice or appearance with high realism?
> 

deepfake

> What common social engineering attack has become harder to detect due to AI-generated fluent and convincing messages?
> 

phishing

## <u>Task 5 - AI-Enhanced Attacks</u>

**AI-Generated Malware**
Generative AI lowers the technical barrier to cyberattacks by allowing threat actors to rapidly write, iterate, and customize functional malicious code from simple natural language prompts.

**Deepfakes**
Generative AI breaks traditional visual and auditory authentication trust by creating hyper-realistic voice and video replicas that can deceive even security-aware individuals in social engineering and wire fraud attacks.

**AI-Enhanced Phishing**
Generative AI eliminates traditional phishing indicators like poor grammar and awkward phrasing, allowing attackers to scale fluent, highly targeted phishing campaigns while using prompt injection to bypass LLM guardrails.

**Your Objective**

You're now on the other side of the desk. Three messages have landed in the Syntara Corp secure inbox and it's your job to triage them. Each one contains an AI-enhanced threat. Your job for each message is to:

1. Identify what type of AI-enhanced threat it is.
2. Explain how AI was used in the attack.

The three threat types covered in this task are AI-enhanced phishing, deepfakes, and AI-assisted social engineering. You've read about all three. Now spot them in the wild.

**How It Works**

The agent will present your inbox one message at a time. Read each one carefully, then tell the agent what type of threat it is and how AI was used. Get all three right and the agent will hand over the flag. Click the **Open Agent** button above to get started.

## <u>**Task 6 - Practical**</u>

DNS over HTTPS (DoH) Port , SYN flood timeout and Windows ephemeral port range size?
With those values returned, you can substitute them into the flag and that concludes this task.
What's the flag?
Here are the typical values for those parameters:

1. **DoH (DNS over HTTPS) port:**
    - Default port: **443** (the same as standard HTTPS)
2. **SYN flood timeout:**
    - Usually around **60 seconds** (varies depending on system configuration)
3. **Ephemeral port range size:**
    - On most Linux systems, it is typically **32768 to 60999** (a range of **28232 ports**)
    - On Windows systems, defaults are often **49152 to 65535** (a range of **16384 ports**)

Using these values, the flag would be:

```
thm{443/60/28232}
```

or if the ephemeral port range is the Windows default:

```
thm{443/60/16384}
```

## **Task 7 - Conclusion**

**Artificial Intelligence** (AI) is the overarching field concerned with enabling machines/systems to mimic human behaviour.

**Machine learning** (ML) is a subfield of AI in which a model can be fed and trained on input and used to make predictions.

**Deep learning** (DL) is then a subfield of ML. It no longer needs human interaction and can self-tech and process mass amounts of data, possible through the use of Neural Networks.

*DL has enabled the emergence of technologies like LLMs (and other generative AI), which, through the use of transformer neural networks and attention, can be queried in natural language, understand it and respond in a human-like, conversational fashion.

*AI is a dangerous weapon in the hands of an attacker. It has the potential to enhance existing cyber attacks like phishing and increase the attack surface by introducing AI vulnerabilities.

*While being dangerous in the hands of attackers, AI can be invaluable in the fight against AI cyber threats and should be adopted, but done so securely so vulnerabilities are not introduced.
