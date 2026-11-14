# AI Security 1.5. AI Forensics

### <u>Task 1 - Introduction</u>

While traditional digital forensics relies on analyst expertise under strict time constraints, integrating AI tools presents both critical opportunities for scaling investigation efficiency and major ethical, legal, and reliability challenges.

### <u>**Task 2 - The AI Forensics Landscape**</u>

- **Data Processing:** The field of Digital Forensics is one ripe with tasks requiring the investigator/analyst to process vast amounts of data in search of evidence or environmental clues. This data processing can be a massive undertaking for an investigator and requires a lot of manual work and time. Now armed with the power of parallelised deep learning & Transformer models, which can process entire bodies of text in parallel (often in mere milliseconds), providing insights and classifications on the processed data.
- **Anomaly Detection:** Analysts are tasked with identifying attacks, and this can often be a “needle in a haystack” scenario, especially sophisticated attacks which can (and often are designed to) blend in with normal activity. Machine Learning algorithms can learn what “normal” behaviour looks like for users, systems and networks, allowing supervised and unsupervised models to identify potential anomalies that could indicate malicious behaviour. Turning the haystack into a handful of hay. This ability to recognise patterns on such a large scale can also mean patterns are spotted that humans can't comprehend.
- **Scalability:** Modern infrastructures can take many different forms, such as cloud, hybrid, remote endpoints, etc., generating more forensic data than ever.  systems can scale effortlessly, continually processing and learning from millions of events across these large and distributed environments, enabling  teams to cover more ground without a proportional increase in workload.

**AI in the Wild**

| **DFIR Task** | **What AI/ML Enables** | **Example Tools / Platforms** | **How AI Solves It** |
| --- | --- | --- | --- |
| **Anomaly Detection / UEBA** | Flags unusual user/system behavior compared to learned “normal” | [Splunk UEBA(opens in new tab)](https://www.splunk.com/en_us/blog/learn/user-entity-behavior-analytics-ueba.html), [Elastic ML(opens in new tab)](https://www.elastic.co/elasticsearch/machine-learning), [Exabeam(opens in new tab)](https://www.exabeam.com/) | AI uses unsupervised learning techniques (like Isolation Forests and Autoencoders) to learn baseline behaviour for users and systems. Deviations from this baseline are flagged as potential threats, even without predefined rules. |
| **Phishing & Communication** | Detects phishing emails and flags risky language in chat/email logs | [Microsoft Defender for O365(opens in new tab)](https://www.microsoft.com/en-us/security/business/siem-and-xdr/microsoft-defender-office-365), [Splunk NLP(opens in new tab)](https://splunkbase.splunk.com/app/6792) | Transformer-based language models (e.g., BERT, RoBERTa) classify messages as phishing or benign based on tone, structure, and known attack patterns. These models help detect impersonation, urgency phrases, and malicious links. |
| **Malware / File Classification** | Classifies files as malicious or benign based on extracted static/dynamic features | [Microsoft Defender (STAMINA)(opens in new tab)](https://www.cyberdefensemagazine.com/stamina-a-new-approach-to-malware-detection-by-microsoft-intel/), [Cylance(opens in new tab)](https://arcticwolf.com/cylance/), [VirusTotal ML integrations(opens in new tab)](https://blog.virustotal.com/2023/04/introducing-virustotal-code-insight.html) | AI systems analyse file metadata, code signatures, and sandbox behaviour to detect threats. These models are trained on large malware corpora to classify new files based on patterns learned from known threats. |
| **Alert Triage & Prioritisation** | Automatically scores, ranks, and filters alerts to reduce analyst workload | [Cortex XSOAR/XSIAM(opens in new tab)](https://start.paloaltonetworks.com/introducing-cortex-xsiam-3), [IBM QRadar Advisor(opens in new tab)](https://www.ibm.com/docs/en/qradar-common?topic=apps-qradar-advisor-watson-app&), [CrowdStrike Falcon + Charlotte AI(opens in new tab)](https://www.crowdstrike.com/en-us/platform/charlotte-ai/) | AI analyses past alert data, analyst feedback, and incident outcomes to rank alerts by severity and relevance. This reduces noise and surfaces the most urgent issues first, saving analysts time, a valuable resource in DFIR. |
| **Timeline & Event Correlation** | Reconstructs attack timelines by clustering and linking logs across sources | [Timesketch(opens in new tab)](https://timesketch.org/), [Velociraptor(opens in new tab)](https://docs.velociraptor.app/docs/overview/), [Jupyter-based analysis(opens in new tab)](https://github.com/jupyterlab/jupyter-ai) | AI clusters similar log events, identifies causal relationships, and aligns activity across systems. This helps analysts visualise attack chains and reconstruct incident timelines faster. Again, time is a valuable resource , and one of the key benefits of integrating AI into DFIR. |

**AI Limitations:** Modern AI models are probabilistic rather than deterministic, they generate outputs based on statistical probabilities learned from data, naturally producing varied responses to the same prompt unless specifically configured otherwise.

<img width="595" height="381" alt="image" src="https://github.com/user-attachments/assets/30f06780-af8a-4b1a-9b74-a11c2074dbae" />


Important metrics to consider when  evaluating AI performance are:

- **Accuracy:** Accuracy refers to the overall rate of correct predictions. It’s very important when assessing an AI ’s performance to not only consider accuracy, as in isolation, it can be very misleading. Especially in a field such as digital forensics, where we often deal with highly imbalanced data (a few malicious files amongst thousands of benign ones, etc.), so a model can achieve a high accuracy by simply predicting the majority class each time.
- **Precision:** Precision measures how often a model's positive predictions are correct. In other words, out of all of the files flagged as malicious, how many are actually malicious? Higher precision = fewer false positives (saving time chasing down irrelevant leads). However, this metric also suffers in isolation as the model can choose to be very selective, only flagging the very obvious malicious files, meaning some malicious files may be missed.
- **Recall:** Recall measures how successful the model was in identifying all positives in the provided dataset. e.g. identifying 18 out of 20 malicious files present would result in a 90% recall. Again, in isolation, this metric would be ineffective as a model could simply cast a wide net, flagging every file as malicious, achieving 100% recall but flagging many false positives in the process.

The Garbage In, Garbage Out (GIGO) principle means an AI trained on flawed data will confidently output false predictions, demanding extreme caution when applying probabilistic models to digital forensics and judicial outcomes.

While AI serves as a force multiplier for rapid data ingestion and pattern identification, its inherent non-determinism, susceptibility to hallucinations, and lack of contextual reasoning mean it cannot replace human expert judgment, formal validation, and strict chain-of-custody standards in legal proceedings.

> What ability of AI helps turn a DFIR investigator by recognising patterns they might not have been able to comprehend?
> 

**Anomaly Detection**

> Which metric tells you the proportion of positively flagged results that were actually correct?
> 

**Precision**

> What term describes the AI characteristic where the same input may yield different outputs across different runs?
> 

**non-determinism**

### <u>**Task 3 - AI & DFIR**</u>

Convolutional Neural Networks (CNNs) automate feature extraction through spatial filtering, enabling advanced digital image, video, and audio forensic analysis within DFIR workflows. Examples of how this can be used in the field include:

- **CNN Based Forgery Detection:** Researchers have started combining traditional forensics methods such as ELA (Error Level Analysis, a technique used in image forensics to detect areas of an image that may have been digitally altered) with  models to identify image tampering.
- **Deepfake Detection**: The advancement of  technologies has also meant advancements for the attacker. Deepfakes are one area which has seen a dramatic increase in quality, and as a forensics analyst, this is yet another area where new challenges are presented.
- **GANs**: Another exciting development in image and video forensics is the use of Generative Adversarial Networks (GANs), a setup where two neural networks compete: one generates fake media, and the other tries to detect it.

**Communication Analysis:** Communication analysis (and other areas of DFIR, for that matter) involves the processing and analysis of large volumes of text. 

- **Phishing Email Detection:** Transformer-based models that are trained for NLP (Natural Language Processing), such as  and RoBERTa, excel at identifying  emails. This technology not only means we can move away from a rule-based approach for detecting incoming  emails (which looked for known bad URLs or keywords) and adopt deep learning, context-aware models but also means it can be used to categorise emails for **human review**, saving a lot of time.
- **Chat Log and Social Media Analysis:** The same technology mentioned above is also harnessed by some forensic platforms, allowing forensic investigators to automatically scan chats for keywords or patterns related to threats (like discussing of nefarious plans) and perform **sentiment analysis** to gauge emotional tone.

**Timeline Reconstruction and User Behavior:** Reconstructing incident timelines is a common and critical part of an investigation; it is also a very labour-intensive and time-consuming task. Machine learning is now being harnessed to help us with this task:

- **Automated Event Timeline Reconstruction:**  systems are particularly adept at correlating **time-sequenced** data from multiple sources and putting together what happened before, during, and after an incident. For example,  algorithms can ingest logs, filesystem timestamps, network records, etc., and automatically build a chronological timeline of events or merge events from server logs,  alerts and application logs into a unified timeline.
- **Anomaly Detection:**  is incredibly good at identifying patterns. In , this ability can give you an eagle eye, flagging things like impossible logins, where a user was logged in at two places simultaneously, or behaviour unusual for this specific user.

**Malware Detection/Analysis:** AI/ML has also lent itself greatly to the area of Malware detection and analysis, with research into this subject looking to provide an edge over traditional analysis methods and signature-based detection:

- Breakthroughs in being able to represent malware files in ways processible by deep neural networks have made it possible to **classify a file** as malicious or benign ([e.g. Microsoft and Intel's STAMINA project](https://www.cyberdefensemagazine.com/stamina-a-new-approach-to-malware-detection-by-microsoft-intel/)).
- ML is also being considered for use in **Dynamic Analysis**, observing how a program behaves to identify whether it is malicious or not.
- Using some form of / is now very common in antivirus and endpoint detection response products.

AI accelerates pattern discovery and media forensics in DFIR, but human expertise remains essential for validation and legal evidence.

> What type of neural network is commonly used in image and video forensics due to its ability to learn spatial patterns in visual data? ****
> 

**Convolutional Neural Network**

> What kind of analysis can be performed on social media or chat logs to assess the emotional tone of messages?
> 

**Sentiment Analysis**

> What type of data do AI systems correlate to reconstruct the timeline of an incident automatically?
> 

**Time-Sequenced**

> What type of analysis observes how a program behaves to determine whether it is malicious, e.g., using its API call sequence?
> 

Dynamic Analysis

### <u>**Task 4 - AI Legal & Ethical Implications**</u>

To properly navigate the legal and ethical complexities of integrating AI into Digital Forensics and Incident Response (DFIR), forensic analysts and legal teams must address four core operational pillars. n this task, we will explore these issues.

**Explainability and Transparency:** Without explainability and expert validation, AI-generated insights fail to meet evidentiary admissibility standards like the U.S. *Daubert* test, making human expertise necessary to defend findings in court.

**Bias and Fairness:** Machine learning models reflect the skewed representations of their training data, which can lead to excluded evidence legally and ethical breaches if algorithmic bias distorts forensic prioritization or misidentifies individuals. What this means is that:

1. Legally, if a defence can show an  technique is biased, judges may exclude its results.
2. Ethically, forensic experts have a duty to validate and correct biases present in  tools (for example, using diverse training data or bias mitigation techniques). 

**Accountability and Chain of Custody:** Digital evidence requires an unbroken, traceable audit trail; using controlled, on-premises systems instead of opaque cloud services preserves chain-of-custody requirements under legal scrutiny.

**Privacy and Data Protection:** Processing sensitive evidence via public cloud servers risks violating privacy laws (such as GDPR) and seal orders, making offline environments or federated learning essential to prevent evidence from being ruled inadmissible.

**The Verdict:** AI is a powerful productivity multiplier for DFIR, but investigators bear a strict legal and ethical duty to apply it within established guidelines to uphold justice and evidence integrity.

> What legal test used in the U.S. assesses whether expert or scientific testimony is admissible in court?
> 

**Daubert**

> What term describes AI models whose internal decision-making processes are difficult to interpret?
> 

**Black Box**

> What real-world technology used by law enforcement has been shown to produce racially biased results in identifying suspects?
> 

**Facial Recognition**

> What technique allows machine learning to be performed without transferring sensitive data to a central server, helping preserve privacy?
> 

**Federated Learning**

### <u>**Task 5 - Practical - The Digital Trail**</u>

Activated the virtual environment. As a forensic investigator, isolation is key; you’ll be working in a virtual environment to ensure any tools you run or changes you make don’t contaminate the original evidence. Think of it as your digital crime scene suit.

```bash
source /opt/dfir-env/bin/activate 
python3 /opt/dfir-lab/classify_logs.py /var/log/auth.log

(dfir-env) ubuntu@tryhackme:~$ python3 /opt/dfir-lab/classify_logs.py /var/log/auth.log
 Failed password for invalid user admin from 192.168.0.100 port 22 ssh2
 Accepted password for j.morgan from 192.168.0.100 port 22 ssh2

python3 /opt/dfir-lab/file_anomalies.py

[*] Scanning paths:
    - /opt/robbco
    - /tmp
    - /dev/shm
    - /usr/local/bin

[!] Supervised AI-flagged suspicious files:
                                                 path  size   entropy
0                    /opt/robbco/sys/boot_monitor.log    59  4.535458
1   /opt/robbco/engineering/MFBootAgent/mfboot_main.c    97  5.122591
2           /opt/robbco/firmware/RETROS_BIOS/core.asm    54  4.358886
3                               /tmp/dock-replace.log   235  4.820192
4                                         /tmp/.syncd    87  4.492156
5                                             /tmp/.x    52  4.411602
6                               /tmp/invoice_dump.txt   709  4.653766
10                   /dev/shm/.core_dump_2025.tgz.enc   369  5.766267
14                              /usr/local/bin/sysmon    52  4.411602

```

Checked the directory above by one by with cat command

The command `source /opt/dfir-env/bin/activate` is used to activate a Python virtual environment named `dfir-env` located in the `/opt/` directory on **Linux**

The command `python3 /opt/dfir-lab/classify_logs.py` executes a Python script designed to **classify build logs** for software packages, specifically identifying error types such as missing dependencies, test failures, or syntax errors.**/var/log/auth.log** is the primary text file used by Debian-based Linux distributions (such as Ubuntu) to record security and authentication events.

```bash
ubuntu@tryhackme:~$  ls /home/
ubuntu@tryhackme:~$ vim python3 /opt/dfir-lab/file_anomalies.py
ubuntu@tryhackme:~$ ls -la /tmp/
ubuntu@tryhackme:~$ cat /tmp/invoice_dump.txt
ubuntu@tryhackme:~$ cat /tmp/.syncd
```

```bash
/home/j.morgan/   <Stores collected recon data> 
ls Documents/Invoice
libreoffice Documents/Invoices/invoice.ods
```

cat .bash_history
sudo nano /home/r.house/.ssh/auth_keys

The command `cat /dev/shm/.core_dump_2025.tgz.enc` will attempt to read and display the contents of a file named `.core_dump_2025.tgz.enc` located in the `/dev/shm` directory.

- **Location**: `/dev/shm` is a **tmpfs** (temporary file storage filesystem) mounted on RAM, meaning the file exists in memory.
- **Content**: The `.tgz.enc` extension suggests the file is an **encrypted tarball**. If the file exists and the user has read permissions, `cat` will output its raw binary data to the terminal, which will appear as **garbled text** or non-printable characters.
- **Persistence**: Since `/dev/shm` is volatile, the file will be **lost** if the system reboots or if the tmpfs is unmounted.

cat /dev/shm/.core_dump_2025.tgz.enc
echo hash '' ] base64 -d

base64 -d /dev/shm/.core_dump_2025.tgz.enc > /tmp/stolen.tar.gz

mkdir /tmp/stolen_source
tar -xzvf /tmp/stolen.tar.gz -C /tmp/stolen_source

The command `tar -xzvf /tmp/stolen.tar.gz -C /tmp/stolen_source` extracts the contents of the compressed archive `/tmp/stolen.tar.gz` into the directory `/tmp/stolen_source`.

- **x**: Extracts files from the archive.
- **z**: Decompresses the archive using gzip (required for `.tar.gz` files).
- **v**: Displays verbose output, listing the files as they are extracted.
- **f**: Specifies the archive file (`/tmp/stolen.tar.gz`).
- **C**: Changes to the specified directory (`/tmp/stolen_source`) before extracting.

<u>**Phase I: Initial Access**</u>

| **Artefact** | **Behaviour** | **Impact/Analysis** | **Explanation** |
| --- | --- | --- | --- |
| `/tmp/invoice_dump.txt` | Stores collected recon data | Reveals prior SSH usage, usernames, and active sessions | Output of the macro’s system recon. Provided the attacker insight into viable accounts and access paths. |

<u>**Phase II: Tooling and Infrastructure**</u>

| **Artefact** | **Behaviour** | **Impact/Analysis** | **Explanation** |
| --- | --- | --- | --- |
| `/tmp/.syncd` | Connects to `http://10.0.0.66/payload.sh` and executes it | Initiates second-stage download | First-stage dropper used to quietly retrieve additional malicious tooling. |
| `/tmp/.x` | Reverse shell stub | Establishes remote shell to `10.0.0.66:4444` | Gave the attacker live access under `j.morgan`’s user context. Likely deployed immediately after the phishing document was opened. |

<u>**Phase III: Privilege Escalation**</u>

| **Artefact** | **Behaviour** | **Impact/Analysis** | **Explanation** |
| --- | --- | --- | --- |
| `/home/j.morgan/.bash_history` | Reveals use of `sudo` to modify SSH keys | SSH key planted in `r.house`’s `authorized_keys` | Subtle privilege escalation — no exploits used. Demonstrates abuse of legitimate permissions for escalation. |

**Phase IV: Disguise and Persistence**

| **Artefact** | **Behaviour** | **Impact/Analysis** | **Explanation** |
| --- | --- | --- | --- |
| `/usr/local/bin/sysmon` | Outbound connection to `10.0.0.66:5555` | Reverse shell disguised as system monitoring tool | Persistence through deception — masks itself as a legitimate binary to evade detection. |
| `/opt/robbco/sys/boot_monitor.log` | Fake boot telemetry logs | Justifies `sysmon`'s presence | Fabricated log file used to support the ruse of a legitimate monitoring utility. |

<u>**Phase V: Source Code Theft**</u>

| **Artefact** | **Behaviour** | **Impact/Analysis** | **Explanation** |
| --- | --- | --- | --- |
| `/opt/robbco/engineering/MFBootAgent/mfboot_main.c/opt/robbco/firmware/RETROS_BIOS/core.asm` | Flagged by ML model | Not malicious, but classified as suspicious | AI mistakenly flagged these — they are RobbCo’s proprietary source code. |
| `/dev/shm/.core_dump_2025.tgz.enc` | Base64-encoded stolen archive | Exfiltration-ready package containing RobbCo IP | Stored in shared memory — stealthy staging location for data theft. |
1. At what time does the attacker successfully log in as j.morgan?
    
    python3 /opt/dfir-lab/classify_logs.py /var/log/auth.log
    Answer : 03:01:02
    
2. What attack method was used to gain initial access?
Answer : Phishing
3. Can you find the attacker’s email address?
Answer : [akeane@poseidonenergy.net](mailto:akeane@poseidonenergy.net)
cat /home/j.morgan/Mail/inbox/email_invoice.eml
4. What command did the attacker run as j.morgan to gain access to the r.house account?
Answer : sudo nano /home/r.house/.ssh/authorized_keys
Analyze /tmp/invoice_dump.txt to get the answer
5. What is the full path of the archive used to steal RobbCo’s source code?
Answer : /dev/shm/.core_dump_2025.tgz.enc

### <u>**Task 6 - Conclusion**</u>

**A Bright Light on the AI Forensics Landscape: C**overed the undeniable potential AI has to help forensic analysts in the pursuit of justice. From understanding how intelligent systems can enhance our abilities to comprehending the responsibility that’s placed on us when we use them to aid in our investigations, here’s what we’ve covered in this lesson:

- How / enhances  tasks like anomaly detection,  analysis, and timeline reconstruction
- The strengths and limitations of models used in forensics (e.g. probabilistic behaviour, precision-recall trade-offs)
- The legal and ethical challenges  introduces, including explainability, chain of custody, and fairness
- A hands-on case investigation where you worked alongside an  assistant powered by scikit-learn to uncover a targeted breach at RobbCo

AI is not a replacement for human insight. In fact, human insight has never been more important than it is now, as the rapid adoption of these AI systems grows.
