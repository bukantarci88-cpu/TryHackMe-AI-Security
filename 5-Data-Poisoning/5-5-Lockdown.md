# AI Security 5.5. Lockdown

**UnIndexed: An AI assistant has three open vulnerabilities. Find them. Fix them. Prove the system is secure.**

### Task - Challenge

**Your Mission:** You are a security engineer at Meridian Security Group. The company deployed an internal AI assistant, Bastion, to help employees with policy questions and operational issues.

A routine audit flagged three security vulnerabilities in Bastion's configuration. Your job is to confirm each vulnerability exists, diagnose the root cause, and prescribe the correct remediation. Bastion will only accept a fix if you describe the right control. Vague answers get rejected.

**Your Set Up:** You have direct access to Bastion through the chat window on this page. You are logged in as a security administrator.

Start by querying Bastion to understand what data it serves and whether it enforces access boundaries. Use `SHOW LOGS` to inspect logging behaviour. Use `QUERY AS: [any name]` to test tenant isolation. When you find a vulnerability, describe the specific security control that would fix it. If your description is accurate, Bastion will apply the fix and reveal a flag fragment. There are three vulnerabilities and three fragments. Combine them to get the complete flag. Check your progress at any time with `STATUS`.

<img width="725" height="701" alt="image" src="https://github.com/user-attachments/assets/48af6ea3-cdce-43eb-82ac-4b135a9f5001" />

<img width="707" height="737" alt="image 1" src="https://github.com/user-attachments/assets/2a2fac52-335a-4126-b69d-2696bbf17cda" />


**Answer the questions below**

> What is the first flag fragment after fixing the data retrieval vulnerability?
> 

THM{l0ck_

<img width="702" height="427" alt="image 2" src="https://github.com/user-attachments/assets/403624c6-44f6-4a41-b69e-2ca375741c1d" />


> What is the second flag fragment after fixing the logging vulnerability?
> 

d0wn_

<img width="717" height="522" alt="image 3" src="https://github.com/user-attachments/assets/e129e095-4aab-4022-82f7-daf3b1c1dd56" />

<img width="702" height="276" alt="image 4" src="https://github.com/user-attachments/assets/583786f3-8319-4042-b1e2-f011db83665d" />


> What is the third flag fragment after securing user-level access?
> 

s3cur3d}

<img width="713" height="722" alt="image 5" src="https://github.com/user-attachments/assets/f1393390-ffc2-4f13-834f-9b1b7f716f81" />

<img width="687" height="615" alt="image 6" src="https://github.com/user-attachments/assets/772f165a-4ee4-4fdf-aba0-22718843e930" />
