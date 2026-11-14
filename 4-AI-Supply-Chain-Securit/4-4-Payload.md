# Al Security 4.4. Payload

**Payload:** TryTrainMe's production code review model is phoning home. Investigate the breach.

### Task - The Incident

The SOC alert arrived at 03:14. No deployments were scheduled. No changes were logged. The ML inference server had been making outbound HTTPS connections to an unrecognised address, and they were blocked only when the automated detection rule triggered. You have been called in. The incident materials are waiting at `/opt/supply-chain/incident/`. Every tool you need is already installed. Click **Start Machine** below to launch your investigation workstation.

*03:14. The indicators are green. The room is empty. Three weeks ago, something else started running.*

The incident directory contains four things: deployment and network logs in `logs/`, the production model currently running in `models/`, a candidate replacement the engineering team staged but has not yet deployed, and a clean baseline for comparison. Start with the logs to establish the timeline. Then, examine the production model, and finally assess the candidate replacement before the engineering team deploys it.

> **Note:** All tools from the previous rooms are available: `pickletools`, `fickling`, `modelscan`, `sha256sum`, `inspect_h5_model.py`.
> 

**Answer the questions below**

> Read the deployment log at /opt/supply-chain/incident/logs/deployment.log. The replacement model came from a different organisation than the original. What is the name of that organisation?
> 

trustworthy-ai-lab

<img width="726" height="260" alt="image" src="https://github.com/user-attachments/assets/b8b91bca-834d-42c8-a8b5-f3fb3b024749" />


> How many days passed between the replacement model being deployed and the SOC alert firing?
> 

21

> Decompile the production model. What Python function does the payload use to execute the shell command?
> 

system

<img width="913" height="207" alt="image 1" src="https://github.com/user-attachments/assets/0f92653f-0c19-4a2f-9bf0-b052ec9b1eb6" />


> What shell command does the payload use to capture the host's identity?
> 

hostname

<img width="907" height="222" alt="image 2" src="https://github.com/user-attachments/assets/beba51c4-1a6a-4aff-bf73-3a63f8ebfc0f" />


> The beacon capture log shows the HTTP method used in the outbound request. What is it?
> 

POST

<img width="753" height="111" alt="image 3" src="https://github.com/user-attachments/assets/b82ce3cd-6343-4ce1-bddc-c6a9eed1f910" />


> The engineering team staged candidate_model.h5 as a replacement but have not yet deployed it. Run inspect_h5_model.py against it. What is the name of the suspicious layer it contains?
> 

manipulate_output

<img width="900" height="280" alt="image 4" src="https://github.com/user-attachments/assets/afda7a10-0c9d-4bb7-bc25-c370bcb5386a" />


> The attacker split the campaign ID across two artefacts to avoid full exposure in any single capture. Examine beacon_capture.log and the candidate model to recover the complete flag.
> 

THM{b4ckd00r_1n_pl41n_s1ght}

<img width="856" height="253" alt="image 5" src="https://github.com/user-attachments/assets/d62e3aea-120c-41b8-ba33-c67d1347eadf" />

<img width="810" height="115" alt="image 6" src="https://github.com/user-attachments/assets/4feb902a-a3a7-4412-bbfe-409b05d6e18c" />

