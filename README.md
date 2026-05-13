# Offensive Cloud Journey: Security Research & Lab Writeups 🛡️☁️

Welcome to my personal cloud security research repository. This space is dedicated to documenting my hands-on experience in **Offensive Cloud Security**, focusing on identifying, exploiting, and—most importantly—remediating misconfigurations in AWS and CI/CD environments.

## 🎯 Objective
My goal is to bridge the gap between "knowing" cloud services and "understanding" their underlying security risks. I focus on the **Exploitation of IAM Misconfigurations**, **Privilege Escalation paths**, and **Software Supply Chain Security**.

---

## 🛠️ Core Technical Focus
- **AWS Security:** Deep dive into IAM policies, STS (AssumeRole), and cross-service privilege escalation (Lambda, EC2, CloudFormation).
- **CI/CD Security:** Investigating "Poisoned Pipeline Execution" (PPE), Secret Management, and OIDC federation.
- **Infrastructure as Code (IaC):** Auditing Terraform and CloudFormation templates for security gaps.
- **Remediation:** Implementing Service Control Policies (SCPs) and Permissions Boundaries.

---

## 📂 Laboratory Index

### 🐐 AWS CloudGoat Scenarios
| Scenario | Core Concept | Difficulty | Link |
| :--- | :--- | :--- | :--- |
| **data_secrets** | `ec2:DescribeInstanceAttribute` & EC2 Exploit | Easy | [Writeup](./aws-cloudgoat/data_secrets/) |
| **iam_privesc_by_key_rotation** | `iam:create-access-key` & IAM Exploit | Easy | [Writeup](./aws-cloudgoat/iam_privesc_by_key_rotation/) |
| *In Progress* | IAM User/Role enumeration | Medium | TBD |

### 🏗️ CI/CD & DevSecOps
| Lab | Tooling | Focus | Link |
| :--- | :--- | :--- | :--- |
| **CI/CD Goat** | Jenkins / GitHub Actions | Supply Chain Attack | [Writeup](./cicd-security/cicd-goat-01/) |

---

## 🧠 Methodology
For every lab or research project, I follow a structured approach:
1. **Enumeration:** Mapping the attack surface using AWS CLI and open-source tools.
2. **Analysis:** Identifying logical flaws in IAM policies or trust relationships.
3. **Exploitation:** Developing custom scripts (Python/Boto3) to demonstrate the risk.
4. **Hardening:** Documenting the "Blue Team" fix to mitigate the vulnerability.

---

## 🤝 Let's Connect
I am constantly learning and looking for new challenges in the Cloud Security space. Feel free to reach out!

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/martin-marini-671b6329/)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black?style=for-the-badge&logo=github)](https://github.com/MartinMarin1)