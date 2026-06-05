# Quiz: Session 2 - Cloud Security & Governance
## Security Mechanisms & Serverless Computing (Discussion Version)

**Total Questions:** 25  
**Time:** 30 minutes  
**Difficulty:** Easy to Moderate

---

### Section A: Security Fundamentals (Q1-5)

**Q1.** In the AWS Shared Responsibility Model, who is responsible for patching the operating system on an EC2 instance?
- A) AWS
- B) Customer
- C) Both equally
- D) Neither

**Answer:** B

**Explanation:** Shared Responsibility Model divides duties:
- **AWS**: Security OF the cloud (physical security, hypervisor, network infrastructure)
- **Customer**: Security IN the cloud (OS patching, firewall rules, data encryption)
For EC2, YOU own the OS, so YOU patch it. For managed services like RDS, AWS patches the OS.

---

**Q2.** **Case Study:** In the 2019 Capital One breach, an attacker accessed 100 million customer records. What was the PRIMARY root cause?
- A) Weak passwords
- B) Excessive IAM permissions on EC2 role
- C) No firewall
- D) Unencrypted data

**Answer:** B

**Explanation:** The EC2 instance had an IAM role with excessive S3 permissions. Attacker exploited a misconfigured WAF → accessed EC2 metadata → got role credentials → downloaded 100M records from S3. Lesson: Always apply least privilege to IAM roles.

---

**Q3.** **Scenario:** A developer requests full admin access "just in case they need it later." What's wrong with this approach?
- A) It costs more money
- B) Violates least privilege - they should only get what they actually need
- C) Admin access is slower
- D) Nothing wrong with it

**Answer:** B

**Explanation:** Least Privilege Principle: Give minimum permissions required for the job. "Just in case" = excess permissions = larger attack surface. If this developer's credentials are compromised, attacker gets full admin access. Instead, start with zero permissions and add only what's needed.

---

**Q4.** A company's S3 bucket was accidentally made public, exposing customer data. Which AWS service could have prevented this?
- A) CloudTrail
- B) GuardDuty
- C) AWS Config (with s3-bucket-public-read-prohibited rule)
- D) Lambda

**Answer:** C

**Explanation:** 
- **CloudTrail**: Logs API calls (detective, not preventive)
- **GuardDuty**: Detects threats (detective)
- **AWS Config**: Monitors compliance rules and can AUTO-REMEDIATE
Config rule `s3-bucket-public-read-prohibited` would flag/prevent public buckets BEFORE data exposure.

---

**Q5.** **Scenario:** An attacker steals an employee's password. What would STOP the attacker from logging in?
- A) Stronger password
- B) Multi-Factor Authentication (MFA)
- C) Longer session timeout
- D) IP whitelisting alone

**Answer:** B

**Explanation:** Password is already stolen, so stronger password doesn't help. MFA requires something you HAVE (phone) + something you KNOW (password). Even with the password, attacker can't log in without the 6-digit code from the employee's phone. MFA blocks 99.9% of credential-based attacks.

---

### Section B: Security Groups (Q6-10)

**Q6.** **Scenario:** Your web server receives a request from a user. The response goes back without creating a new rule. This happens because Security Groups are:
- A) Stateless
- B) Stateful
- C) Bidirectional
- D) Permissive

**Answer:** B

**Explanation:** Stateful means the firewall remembers the connection. If inbound traffic is allowed, the return (response) traffic is automatically allowed without a separate outbound rule. Contrast with Network ACLs which are stateless - you need explicit inbound AND outbound rules.

---

**Q7.** **Scenario:** You create a Security Group allowing inbound SSH (port 22). You notice you can SSH in AND receive responses back without adding an outbound rule. Why?
- A) SSH doesn't need outbound rules
- B) Security Groups are stateful - response traffic is auto-allowed
- C) All outbound is blocked by default
- D) AWS adds rules automatically

**Answer:** B

**Explanation:** Same concept as Q6 - stateful behavior. Your SSH command goes IN (allowed by inbound rule), and the server's response comes OUT automatically because the Security Group tracks this as part of the same connection. No need to manually allow outbound port 22.

---

**Q8.** **Scenario:** A database server should ONLY accept connections from web servers, not from the internet. How do you configure the database security group?
- A) Allow port 3306 from 0.0.0.0/0
- B) Allow port 3306 from web-server security group
- C) Deny all traffic
- D) Allow all traffic from VPC

**Answer:** B

**Explanation:** Security Groups can reference other Security Groups as sources. Instead of IP addresses, use: "Allow 3306 from sg-webserver". This way:
- Web servers (in sg-webserver) can connect
- Internet (0.0.0.0/0) cannot connect
- If web server IPs change, rule still works

---

**Q9.** Which statement about Security Groups is FALSE?
- A) Default: All inbound denied
- B) Default: All outbound allowed
- C) Can create explicit DENY rules
- D) Applied at instance level

**Answer:** C

**Explanation:** Security Groups are ALLOW-ONLY. You cannot create deny rules. Everything not explicitly allowed is implicitly denied. If you need deny rules, use Network ACLs (which support both allow and deny, processed in order).

---

**Q10.** **Use Case:** Your admin panel should only be accessible from your office IP (203.0.113.50). Which security group rule is correct?
- A) Allow 443 from 0.0.0.0/0
- B) Allow 443 from 203.0.113.50/32
- C) Allow 22 from anywhere
- D) Deny all except 203.0.113.50

**Answer:** B

**Explanation:** `/32` means a single IP address (not a range). Rule: "Allow HTTPS (443) from 203.0.113.50/32" means ONLY your office IP can access the admin panel. Option A allows everyone, Option C is SSH not HTTPS, Option D is invalid (no deny rules in SGs).

---

### Section C: IAM (Q11-16)

**Q11.** **Scenario:** A startup has 3 developers who all need identical EC2 + S3 permissions. Instead of creating 3 separate policies, they should:
- A) Share one IAM User account
- B) Create an IAM Group, attach policy, add all 3 users
- C) Use root account for everyone
- D) Create 3 separate policies manually

**Answer:** B

**Explanation:** IAM Groups allow you to manage permissions collectively. Create "Developers" group → attach policy once → add all 3 users to group. When a 4th developer joins, just add them to the group. When permissions change, update the group policy (not 3 individual policies).

---

**Q12.** A CI/CD pipeline needs to deploy code to EC2 but should NEVER delete instances. What IAM concept is best?
- A) IAM User with password
- B) IAM Role with limited policy
- C) Root account
- D) IAM Group

**Answer:** B

**Explanation:** IAM Role (not User) because:
- Roles are for services/applications (no password to manage)
- Limited policy: Allow `ec2:RunInstances`, `ec2:StopInstances` but NOT `ec2:TerminateInstances`
- Principle of least privilege: only deploy permissions, no delete

---

**Q13.** **Scenario:** You write this JSON: `{"Effect": "Allow", "Action": "s3:GetObject", "Resource": "arn:aws:s3:::mybucket/*"}`. What IAM component is this?
- A) User
- B) Group
- C) Role
- D) Policy

**Answer:** D

**Explanation:** This is a Policy document (JSON format). IAM Policies define permissions:
- **Effect**: Allow or Deny
- **Action**: What API calls are permitted (s3:GetObject)
- **Resource**: Which resources (mybucket and its contents)
Policies are attached to Users, Groups, or Roles.

---

**Q14.** **Best Practice Question:** Why should you NEVER use the root account for daily tasks?
- A) It's slower
- B) It has unlimited permissions; if compromised, everything is lost
- C) It costs more
- D) It doesn't support CLI

**Answer:** B

**Explanation:** Root account has UNRESTRICTED access - can delete all resources, change billing, close the account. If compromised, attacker has god-mode. Best practice: Create IAM admin user, enable MFA on root, lock root credentials away, only use for account-level tasks (billing, closing account).

---

**Q15.** An intern joins for 3 months and needs read-only access to a specific S3 bucket. Best approach?
- A) Share root credentials
- B) Create IAM user with S3ReadOnly policy scoped to that bucket
- C) Give full S3 access
- D) Create an admin user

**Answer:** B

**Explanation:** Least privilege in action:
- Create dedicated IAM user (not shared credentials)
- Attach S3ReadOnlyAccess policy scoped to specific bucket (not all S3)
- When intern leaves, delete the user
Never share root credentials or give more access than needed.

---

**Q16.** **Scenario:** An employee's laptop is stolen. Their AWS password is compromised. If they had _______ enabled, the attacker still couldn't log in without their phone.
- A) Longer password
- B) Access Keys
- C) MFA
- D) VPN

**Answer:** C

**Explanation:** MFA (Multi-Factor Authentication) requires:
1. Something you KNOW (password) - compromised
2. Something you HAVE (phone with authenticator app) - attacker doesn't have
Even with the password, login fails without the 6-digit code from the phone. Always enable MFA on all human users.

---

### Section D: Data Security (Q17-19)

**Q17.** **Scenario:** A hacker intercepts data while it travels from user's browser to your S3 bucket. This attack targets data:
- A) At Rest
- B) In Transit
- C) In Use
- D) In Storage

**Answer:** B

**Explanation:** Three data states:
- **At Rest**: Data stored on disk (S3, EBS) - protect with encryption (SSE)
- **In Transit**: Data moving over network - protect with TLS/HTTPS
- **In Use**: Data being processed in memory
The interception attack = man-in-the-middle = targets data IN TRANSIT. Solution: Always use HTTPS.

---

**Q18.** **Scenario:** A developer wants to encrypt S3 data but doesn't want to manage encryption keys at all. Easiest option?
- A) SSE-KMS (requires key management)
- B) SSE-S3 (AWS manages everything)
- C) SSE-C (customer provides keys)
- D) Client-side (encrypt before upload)

**Answer:** B

**Explanation:** 
- **SSE-S3**: AWS creates, manages, rotates keys. Zero effort from you.
- **SSE-KMS**: You create keys in KMS, more control but more work
- **SSE-C**: You provide keys with every request
- **Client-side**: You encrypt before upload
For "zero key management" → SSE-S3 is the simplest.

---

**Q19.** **Compliance Scenario:** A healthcare app stores patient records in S3. Regulations require encryption + audit trail of who accessed data. Best encryption option?
- A) SSE-S3
- B) SSE-KMS (provides audit trail via CloudTrail)
- C) No encryption
- D) SSE-C

**Answer:** B

**Explanation:** SSE-KMS provides:
- Encryption (compliance requirement ✓)
- CloudTrail logs every key usage (who decrypted what, when) - audit trail ✓
- Key rotation policies
SSE-S3 encrypts but doesn't log key usage. For compliance (HIPAA, PCI), SSE-KMS is required.

---

### Section E: Security Monitoring (Q20-22)

**Q20.** **Scenario:** Your manager asks "Who deleted the production S3 bucket last night at 2 AM?" Which service has this information?
- A) GuardDuty
- B) CloudTrail
- C) CloudWatch
- D) Inspector

**Answer:** B

**Explanation:** CloudTrail logs ALL API calls:
- WHO: Which IAM user/role
- WHAT: Which API (DeleteBucket)
- WHEN: Timestamp
- FROM WHERE: Source IP
Query CloudTrail for `DeleteBucket` event around 2 AM to find the culprit. CloudWatch monitors metrics, GuardDuty detects threats, Inspector scans for vulnerabilities.

---

**Q21.** **Scenario:** Your EC2 instance suddenly has 100% CPU usage. Investigation reveals it's mining cryptocurrency without your knowledge. Which service would have detected this automatically?
- A) CloudTrail
- B) GuardDuty
- C) CloudWatch (CPU metrics only)
- D) S3

**Answer:** B

**Explanation:** GuardDuty uses ML to detect threats:
- CryptoCurrency:EC2/BitcoinTool.B - detects crypto mining
- Analyzes VPC Flow Logs, CloudTrail, DNS logs
- Would alert: "EC2 instance communicating with known mining pool"
CloudWatch shows CPU is high but doesn't know WHY. GuardDuty identifies the THREAT.

---

**Q22.** **Use Case:** You want to automatically detect if any S3 bucket becomes public. Which service helps?
- A) CloudTrail
- B) GuardDuty
- C) AWS Config
- D) Lambda

**Answer:** C

**Explanation:** AWS Config continuously monitors resource configurations against rules:
- Rule: `s3-bucket-public-read-prohibited`
- If any bucket becomes public → Config flags it as NON_COMPLIANT
- Can trigger auto-remediation (Lambda) to make it private
CloudTrail logs the change but doesn't evaluate compliance. Config evaluates CONTINUOUSLY.

---

### Section F: Lambda Serverless (Q23-25)

**Q23.** **Scenario:** Your function runs 1000 times/day for 200ms each. With EC2, you'd pay for a server running 24/7. With Lambda, you pay for:
- A) 24 hours of compute
- B) Only 200 seconds of actual execution (1000 × 200ms)
- C) Per-user licensing
- D) Monthly flat fee

**Answer:** B

**Explanation:** Lambda pricing = execution time only:
- 1000 invocations × 200ms = 200,000 ms = 200 seconds
- You pay for 200 seconds, not 86,400 seconds (24 hours)
- EC2: Pay even when idle
- Lambda: Pay ONLY when code runs
For sporadic workloads, Lambda can be 90%+ cheaper than EC2.

---

**Q24.** **Use Case:** Every time a user uploads an image to S3, a thumbnail should be automatically generated. Best architecture?
- A) EC2 polling S3 every minute
- B) S3 event triggers Lambda function
- C) Manual processing
- D) Scheduled cron job

**Answer:** B

**Explanation:** Event-driven architecture:
- S3 Event Notification → triggers Lambda on every upload
- Lambda processes image → saves thumbnail back to S3
- No polling (wasteful), no manual work, no cron (delayed)
- Instant processing, scales automatically, pay only for actual processing

---

**Q25.** A Lambda function needs to read from S3 and write to DynamoDB. Instead of embedding AWS credentials in code, you should:
- A) Use environment variables
- B) Attach an IAM Role to Lambda
- C) Hardcode access keys
- D) Use root credentials

**Answer:** B

**Explanation:** NEVER hardcode credentials. IAM Execution Role:
- Attach role to Lambda function
- Role has policy allowing S3 read + DynamoDB write
- Lambda automatically gets temporary credentials
- No secrets in code, no keys to rotate
Environment variables are slightly better than hardcoding but still not ideal. Roles are the AWS-native solution.

---

## Answer Key

| Q | Answer | Q | Answer | Q | Answer | Q | Answer | Q | Answer |
|---|--------|---|--------|---|--------|---|--------|---|--------|
| 1 | B | 6 | B | 11 | B | 16 | C | 21 | B |
| 2 | B | 7 | B | 12 | B | 17 | B | 22 | C |
| 3 | B | 8 | B | 13 | D | 18 | B | 23 | B |
| 4 | C | 9 | C | 14 | B | 19 | B | 24 | B |
| 5 | B | 10 | B | 15 | B | 20 | B | 25 | B |

---

## Scoring Guide
| Score | Grade | Interpretation |
|-------|-------|----------------|
| 23-25 | A | Excellent understanding |
| 20-22 | B | Good grasp of concepts |
| 17-19 | C | Average, review weak areas |
| 14-16 | D | Needs more practice |
| <14 | F | Re-study required |

---

## Topic-wise Performance Tracker

| Section | Questions | Your Score | Max Score |
|---------|-----------|------------|-----------|
| Security Fundamentals | Q1-5 | ___ | 5 |
| Security Groups | Q6-10 | ___ | 5 |
| IAM | Q11-16 | ___ | 6 |
| Data Security | Q17-19 | ___ | 3 |
| Security Monitoring | Q20-22 | ___ | 3 |
| Lambda Serverless | Q23-25 | ___ | 3 |
| **TOTAL** | | ___ | **25** |

---
*Session 2 Quiz (Discussion Version) | Cloud Computing Workshop*
| IAM | Q11-16 | ___ | 6 |
| Data Security | Q17-19 | ___ | 3 |
| Security Monitoring | Q20-22 | ___ | 3 |
| Lambda Serverless | Q23-25 | ___ | 3 |
| **TOTAL** | | ___ | **25** |

---
*Session 2 Quiz | Cloud Computing Workshop*
