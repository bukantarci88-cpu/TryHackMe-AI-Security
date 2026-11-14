# AI Security 1.1. The Building Blocks of AI

### Task 1 - Introduction

The cybersecurity industry is actively evaluating and adapting to the profound impact of Artificial Intelligence.There's a lot of research being done, a lot of questions being asked, and this room is going to start answering them:

- What is AI and Machine Learning (ML)?
- How can it be used in our industry?
- How will it affect my role?
- How are attackers leveraging it?

### Task 2 - Introducing TryHackMe's AI Agent Platform

You reviewed an introduction to the **TryHackMe AI Agent platform**, learning that some tasks use interactive AI agents with specific roles and goals instead of traditional terminals, and that a sandbox agent is available for practice before starting the challenges.

### Task 3 - What is AI and Machine Learning?

Machine Learning is an iterative subfield of AI where computers learn patterns directly from data to make predictions, following a lifecycle that requires ongoing monitoring to prevent issues like overfitting.

<img width="976" height="859" alt="image" src="https://github.com/user-attachments/assets/eaa74f01-f8f0-49ed-9630-6f9294764164" />


**Answer the questions below**

> What is the term for when a model becomes too familiar with its training data and fails to generalise to new data?
> 

Overfitting

> What is the subfield of AI that enables systems to learn from data without being explicitly programmed?
> 

Machine Learning

### Task 4 - Machine Learning Algorithms

**The Brains of the Operation**

Machine learning algorithms build models by iteratively predicting, measuring error, and optimizing performance.

ML algorithms fall into four main categories depending on how they learn and what kind of data they work with.

**Supervised learning** trains on labelled data, meaning every training example comes with the correct answer already attached. The model learns to map inputs to outputs based on those examples. Predicting house prices or classifying whether an email is spam are both supervised learning problems.

**Unsupervised learning** works with unlabelled data and has to find its own structure. Rather than being told what the answer is, the algorithm identifies patterns, clusters, and relationships in the data on its own. It's useful for things like grouping customers by behaviour or detecting anomalies in network traffic.

**Semi-supervised learning** sits between the two. It uses a small portion of labelled data to guide the learning process across a much larger pool of unlabelled data. This is practical in situations where labelling data is expensive or time-consuming.

**Reinforcement learning** works differently from all three. Rather than learning from a fixed dataset, an agent learns by taking actions in an environment and receiving rewards or penalties based on the outcomes. Over time, it refines its behaviour to maximise reward. It's the approach behind things like game-playing AI and autonomous systems.

Your job for each mission is to:

1. Identify which of the four algorithm types applies: **supervised**, **unsupervised**, **semi-supervised**, or **reinforcement learning**.
2. Explain in one sentence why that algorithm type fits the scenario.

Get it right and the next mission unlocks. Complete all four and the final mission will hand over the flag. Click the **Open Agent** button above to receive your first briefing.

**Answer the questions below**

> Which category of ML algorithm learns by receiving rewards or penalties based on actions taken in an environment?
> 

Reinforcement learning

> Which category of ML algorithm uses a small amount of labelled data to guide learning across a larger unlabelled dataset?
> 

Semi-supervised learning

> What's the flag?
> 

THM{4lg0r1thm_4g3nt}

### Task 5 - Neural Networks and Deep Learning

**Deep Learning scales Machine Learning by using multi-layered networks to automatically extract features from massive, unstructured datasets without human labeling.**

**Take Control of NEURON-1**

In the previous task we covered how a neural network processes data layer by layer: raw input goes in, the hidden layers extract features and patterns, and the output layer makes a classification.  

NEURON-1 is a freshly initialised neural network with no training data. You're going to give it something to classify and guide it through each layer manually, the same way a real network processes data during training. The point of the exercise is to make that flow tangible rather than just theoretical.

**What to Classify**

You can give NEURON-1 anything as an input. It doesn't have to be a cyber security example. Here are a few ideas to get you thinking:

- An image of an animal, like a golden retriever
- A line from a firewall log
- An email that might be spam
- A network packet

Pick whatever makes sense to you. Once you've chosen, share it with NEURON-1 and it will guide you through the rest.

**What You'll Be Doing**

NEURON-1 will walk you through three stages:

1. **Input Layer:** You describe the raw features of your input, the basic characteristics the network receives as data.
2. **Hidden Layer:** You identify what patterns those raw features form when combined, the deeper meaning the network extracts. For example, raw features like "four legs, fur, tail" might combine into the pattern "mammal, likely domestic animal".
3. **Output Layer:** Based on what the hidden layer detected, you make the final classification.

Get the flow right and NEURON-1 will reward you with a flag.

Answer the questions below

> What is the first layer in a neural network that receives raw input data?
> 

Input layer

> What term describes the weighted connections between nodes in a neural network?
> 

Synapses

> What's the flag?
> 

THM{n3ur0n_1_0nl1n3}

### Task 6 - Large Language Models

Large Language Models power the modern AI boom by translating years of AI, machine learning, and deep learning research into systems that process and generate natural, human-like text.

**What are LLMs and how do they work?**

Large Language Models (LLMs) are deep learning systems that process and generate text by using billions of numerical parameters—fine-tuned through self-supervised pre-training and backpropagation on massive datasets—to continuously predict the most likely next word in a sequence.

![LLM being fed sentence and making predictions on what last word will be](https://cdn-images.tryhackme.com/user-uploads/6228f0d4ca8e57005149c3e3/room-content/6228f0d4ca8e57005149c3e3-1745606636896.svg)

Artificial Intelligence encompasses Machine Learning, which leverages data to learn patterns; Deep Learning, which scales ML using multi-layered neural networks; and Large Language Models, which apply transformer-based Deep Learning to master human language. 

Answer the questions below

> What type of neural network, introduced by Google in 2017, powers modern LLMs?
> 

Transformer neural networks

> What is the name of the process where humans review and flag model outputs to refine its behaviour after pre-training?
> 

RLHF

> What mechanism do transformer networks use to assign different levels of importance to different words in a sequence?
> 

Attention

> What algorithm is used to adjust a model's parameters based on the difference between its prediction and the correct answer?
> 

Backpropagation

### Task 7 - Practical

You've made it through the building blocks. AI, ML, neural networks, DL, LLMs. Now it's time to put all of it together in one place.

Click the **View Site** button above to launch NEURON-1, your final challenge for this room.

You'll be dropped into an interactive neural network and tasked with operating it from the ground up: feed the input, navigate the hidden layer, and read the output correctly to complete your classification. Everything you need to answer the questions the site asks is in this room. Get it right and the network rewards you with the flag.

**Answer the questions below**

> What's the flag?
> 

THM{y0u_tr41n3d_th3_n3tw0rk}

### Task 8 - Conclusion

At the start of this room it was said that knowledge is power, and that's especially true when it comes to AI. The technology has moved fast enough to leave a lot of people feeling like they missed the memo. Hopefully that's no longer you. Here's a recap of what's been covered:

- **Artificial Intelligence** is the overarching field concerned with enabling machines to mimic human intelligence and reasoning.
- **Machine Learning** is a subfield of AI in which models learn from data to make predictions, following a structured lifecycle from data collection through to deployment and monitoring.
- **ML algorithms** fall into four categories: supervised, unsupervised, semi-supervised, and reinforcement learning, each suited to different types of problems and data.
- **Neural networks** replicate the structure of the human brain using layers of weighted nodes, enabling models to extract increasingly complex features from raw input data.
- **Deep Learning** extends this further, removing the need for labelled data entirely and enabling self-learning at scale through neural networks with more than three layers.
- **Large Language Models** are the product of all of the above. Built on transformer neural networks and trained on vast datasets, they predict the next word in a sequence to generate human-like text, shaped into useful tools through RLHF.
