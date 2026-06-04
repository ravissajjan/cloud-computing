# Cloud Management Mechanisms

## Table of Contents

| Section | Topic | Page |
|---------|-------|------|
| **Basics** | | |
| - | [Key Security Terms Glossary](#key-security-terms-glossary) | Quick reference |
| **Theory** | | |
| 2.1 | [Cloud Security Fundamentals](#21-cloud-security-fundamentals-20-min) | Shared responsibility, threats |
| 2.2 | [Security Groups (Virtual Firewalls)](#22-security-groups-virtual-firewalls) | Network security |
| 2.3 | [IAM (Identity & Access Management)](#23-iam-identity-and-access-management) | Users, roles, policies |
| 2.4 | [Data Security Mechanisms](#24-data-security-mechanisms) | Encryption, DLP |
| 2.5 | [Security Monitoring & Governance](#25-security-monitoring--governance) | CloudTrail, GuardDuty |
| **Hands-On Labs** | | |
| 2.6 | [Lab: Secure AWS Environment](#26-lab-exercise-secure-aws-environment) | IAM, Security Groups |
| 2.7 | [Serverless Computing with Lambda](#27-serverless-computing-with-lambda) | Functions, triggers |
| **Case Study** | | |
| 2.8 | [MediCare Cloud Migration](#28-case-study-medicare-health-insurance-cloud-migration) | Shared responsibility in practice |
| **Wrap-Up** | | |
| - | [Summary & Key Takeaways](#summary-key-takeaways) | Learning outcomes |
| - | [Final Quiz](#final-quiz-test-your-knowledge) | Self-assessment (5 questions) |
| - | [Security Best Practices Checklist](#security-best-practices-checklist) | Pre-production checklist |

---

## Session 2 Overview
| Aspect | Details |
|--------|---------|
| Duration | 3 hours |
| Target | B.Tech/M.Tech CSE students |
| Prerequisites | Session 1 completed, AWS Free Tier account |
| Focus | Security mechanisms, IAM, encryption, monitoring |

---

# Session 2: Security & Governance Mechanisms (3 Hours)

## Key Security Terms Glossary

Refer back to this when you encounter unfamiliar security terms:

| Term | Simple Meaning |
|------|----------------|
| **IAM** | Identity and Access Management - Who can do what in your AWS account |
| **MFA** | Multi-Factor Authentication - Password + phone code (like Google 2FA) |
| **Security Group** | Firewall rules for EC2 instances (like hostel security rules) |
| **NACL** | Network ACL - Firewall at subnet level (building gate vs room door) |
| **Encryption** | Scrambling data so only authorized people can read it |
| **At Rest** | Data stored on disk (like files in your laptop) |
| **In Transit** | Data moving over network (like WhatsApp messages being sent) |
| **KMS** | Key Management Service - AWS manages your encryption keys |
| **Policy** | JSON document defining permissions (like a rulebook) |
| **Role** | Temporary identity that can be assumed (like a costume you wear) |
| **Principal** | Who is making the request (user, service, or account) |
| **ARN** | Amazon Resource Name - Unique ID for any AWS resource |
| **CloudTrail** | Records all API calls (like CCTV footage for your account) |
| **GuardDuty** | AI-powered threat detection (like a smart security guard) |
| **VPC** | Virtual Private Cloud - Your isolated network in AWS |
| **CIDR** | IP range notation (10.0.0.0/16 = 65,536 IP addresses) |
| **Least Privilege** | Give minimum permissions needed - start with zero, add as required |
| **Zero Trust** | Never trust, always verify - assume breach mentality |

> 💡 **Tip:** Bookmark this glossary! Refer back as you read.

---

## 2.1 Cloud Security Fundamentals

### Why Security Matters: The Numbers

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SECURITY BREACH COSTS (2025)                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  💰 Average data breach cost:      $4.88 MILLION                    │
│  💰 Healthcare breach:             $11.5 MILLION                    │
│  💰 Capital One (2019):            $190 MILLION fine                │
│  💰 Equifax (2017):                $700 MILLION settlement          │
│                                                                     │
│  ⏱️ Average time to detect breach: 194 DAYS                         │
│  ⏱️ Average time to contain:       64 DAYS                          │
│                                                                     │
│  "One wrong checkbox in AWS console = millions in damages"          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Shared Responsibility Model

### Student Analogy: Rented Apartment 🏠
```
┌─────────────────────────────────────────────────────────────────────┐
│                    RENTED APARTMENT ANALOGY                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  LANDLORD (AWS) is responsible for:                                 │
│  ├── Building structure (physical security)                         │
│  ├── Main gate security (network infrastructure)                    │
│  ├── Electricity & water supply (hypervisor, compute)               │
│  ├── Fire safety systems (compliance certifications)                │
│  └── Common area maintenance (managed service patching)             │
│                                                                     │
│  TENANT (YOU) is responsible for:                                   │
│  ├── Locking YOUR door (Security Groups, IAM)                       │
│  ├── Who you give keys to (user access management)                  │
│  ├── Protecting valuables inside (data encryption)                  │
│  ├── Not leaving windows open (patching EC2 instances)              │
│  └── What you store inside (application security)                   │
│                                                                     │
│  🚨 If someone breaks in through YOUR unlocked door,                │
│     it's YOUR fault — not the landlord's!                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Shared Responsibility Matrix
```
┌──────────────────────────────────────────────────────────────────────┐
│          CUSTOMER RESPONSIBILITY ("Security IN the Cloud")           │
├──────────────────────────────────────────────────────────────────────┤
│ • Data classification & encryption                                   │
│ • IAM (users, roles, policies)                                       │
│ • OS patching (for EC2)                                              │
│ • Security group configuration                                       │
│ • Application security                                               │
├──────────────────────────────────────────────────────────────────────┤
│          AWS RESPONSIBILITY ("Security OF the Cloud")                │
├──────────────────────────────────────────────────────────────────────┤
│ • Physical data center security                                      │
│ • Network infrastructure                                             │
│ • Hypervisor security                                                │
│ • Managed service patching (RDS, Lambda)                             │
│ • Compliance certifications                                          │
└──────────────────────────────────────────────────────────────────────┘
```

### Responsibility Changes by Service Type

| Service Type | Customer Manages | AWS Manages |
|--------------|------------------|-------------|
| **IaaS (EC2)** | OS, patches, apps, data, firewall | Hardware, hypervisor, network |
| **PaaS (Elastic Beanstalk)** | Code, data | OS, runtime, scaling |
| **SaaS (Gmail, Workmail)** | Data, user access | Everything else |

### The 5 Types of Threat Agents (WHO Attacks?)

```
┌─────────────────────────────────────────────────────────────────────┐
│                    THREAT AGENTS - WHO ATTACKS?                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. EXTERNAL HACKERS 🎭                                            │
│     • Cybercriminals (want money)                                   │
│     • Nation-states (espionage)                                     │
│     • Hacktivists (ideology)                                        │
│     Example: SolarWinds Attack (2020) - Russian hackers             │
│              compromised 18,000+ organizations                      │
│                                                                     │
│  2. MALICIOUS INSIDERS 👤 (MOST DANGEROUS!)                        │
│     • Current/former employees with access                          │
│     • Contractors and vendors                                       │
│     Example: Tesla (2020) - Employee offered $1M to install malware │
│                                                                     │
│  3. ANONYMOUS ATTACKERS 👻                                         │
│     • Use Tor/VPN to hide identity                                  │
│     • Ransomware operators                                          │
│     • DDoS attackers                                                │
│                                                                     │
│  4. TRUSTED THIRD-PARTY 🤝                                         │
│     • Vendors with legitimate access                                │
│     • Supply chain attacks                                          │
│     Example: Target (2013) - Hackers entered via HVAC vendor        │
│              → 40 million credit cards stolen                       │
│                                                                     │
│  5. AUTOMATED THREATS (BOTS) 🤖                                    │
│     • Malware/Ransomware                                            │
│     • 24/7 scanning for exposed credentials                         │
│     • Crypto miners                                                 │
│     Example: Mirai Botnet (2016) - 600K infected IoT devices        │
│              took down Twitter, Netflix, Reddit                     │
│                                                                     │
│  💡 KEY INSIGHT: Insiders are MOST dangerous (already have access)  │
│                  Bots are MOST common (automated 24/7)              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Student Analogy: GitHub Repo Security
```
┌─────────────────────────────────────────────────────────────────────┐
│               YOUR GITHUB REPO SECURITY ANALOGY                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Think of your AWS account like your GitHub repository:             │
│                                                                     │
│  1. EXTERNAL HACKERS    = Random people trying leaked passwords     │
│  2. MALICIOUS INSIDERS  = Team member who deletes main branch!      │
│  3. ANONYMOUS ATTACKERS = DDoS attackers hitting your deployed API  │
│  4. THIRD-PARTY         = npm package that steals .env secrets      │
│  5. BOTS                = Scripts scanning GitHub for AWS keys 24/7 │
│                                                                     │
│  💡 REAL EXAMPLE: In 2022, bots found AWS keys pushed to GitHub     │
│     in under 5 MINUTES and spun up crypto miners costing $50K!      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

### Self-Check: Threat Agent Knowledge

**Q: Which threat agent do you think is most DANGEROUS? And which is most COMMON?**

<details>
<summary>Click to see answer</summary>

**Most Dangerous:** Malicious INSIDERS
- They already have legitimate access
- Know where sensitive data is stored
- Can bypass many security controls
- Example: A disgruntled employee with S3 access can download customer data

**Most Common:** BOTS (Automated threats)
- Scan the entire internet 24/7 automatically
- No human effort required
- Found a leaked AWS key on GitHub? Attack starts in 5 minutes
- Millions of automated attempts per day globally

**Key Insight:** Your biggest threat is often someone who already has access!

</details>

---

### ⚠️ TOP 6 MISTAKES THAT CAUSE BREACHES

```
┌─────────────────────────────────────────────────────────────────────┐
│                AVOID THESE MISTAKES!                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ❌ #1: Using root account for daily work                          │
│      → Root = God mode. One mistake = total disaster                │
│                                                                     │
│  ❌ #2: No MFA (Multi-Factor Authentication)                       │
│      → Password alone can be stolen. MFA = much harder.             │
│                                                                     │
│  ❌ #3: S3 bucket set to "public"                                  │
│      → Your data visible to ENTIRE INTERNET!                        │
│                                                                     │
│  ❌ #4: Security Group allows SSH (port 22) from 0.0.0.0/0         │
│      → Anyone can try to hack your server                           │
│                                                                     │
│  ❌ #5: Giving "AdministratorAccess" to everyone                   │
│      → Intern can delete production database!                       │
│                                                                     │
│  ❌ #6: Hardcoding passwords/keys in code                          │
│      → Gets pushed to GitHub, hackers find it in minutes            │
│                                                                     │
│  🚨 80% of breaches are caused by these 6 mistakes!                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Case Study: Capital One Breach (2019)

**What happened:**
1. Attacker exploited misconfigured WAF
2. Used SSRF to access EC2 metadata (IMDSv1)
3. Retrieved IAM role credentials
4. Downloaded 100M customer records from S3

**Root causes:**
- EC2 role had excessive S3 permissions
- IMDSv1 allowed unauthenticated metadata access
- No anomaly detection for data exfiltration

**Lessons:**
- Use IMDSv2 (require session tokens)
- Apply least privilege to IAM roles
- Enable GuardDuty for threat detection
- Monitor CloudTrail for unusual S3 access

### The Big 7 Cloud Security Threats (With Real Examples)

| # | Threat | Real-World Example | Prevention |
|---|--------|-------------------|------------|
| 1 | **Data Breaches** | Capital One (2019) - 100M records via misconfigured WAF | Least privilege, encryption, Macie |
| 2 | **Account Hijacking** | Twitter Bitcoin Scam (2020) - Obama, Musk accounts hijacked via social engineering | MFA, employee training |
| 3 | **Insecure APIs** | Parler (2021) - 70TB data scraped via insecure API | Authentication, rate limiting |
| 4 | **DDoS Attacks** | AWS (2020) - 2.3 Tbps attack mitigated | AWS Shield, WAF, auto-scaling |
| 5 | **Malware Injection** | Codecov (2021) - CI/CD tool compromised, stole secrets | Pin dependencies, verify checksums |
| 6 | **Data Loss** | GitLab (2017) - Engineer deleted prod DB, backups didn't work! | Test backups, deletion protection |
| 7 | **Shared Tech Vulnerabilities** | Meltdown/Spectre (2018) - CPU flaws affected all clouds | Keep patched, use dedicated hosts |

> **Key Takeaway:**
> "Most breaches aren't sophisticated hacks - they're misconfigurations,
> weak passwords, and human error. The good news? These are PREVENTABLE!"

---

## 2.2 Security Groups (Virtual Firewalls)

### Quick VPC Primer (Before We Start)

> **What is a VPC?** A Virtual Private Cloud is your own isolated network in AWS - like having your own private data center. Security Groups live inside VPCs and control traffic to your resources.

```
┌─────────────────────────────────────────────────────────────────────┐
│                         YOUR VPC (10.0.0.0/16)                      │
│  ┌──────────────────────────────┐  ┌─────────────────────────────┐  │
│  │  Public Subnet (10.0.1.0/24) │  │ Private Subnet (10.0.2.0/24)│  │
│  │  ┌─────────┐ ← web-sg        │  │  ┌─────────┐ ← db-sg        │  │
│  │  │   EC2   │                 │  │  │   RDS   │                │  │
│  │  └─────────┘                 │  │  └─────────┘                │  │
│  └──────────────────────────────┘  └─────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘

Security Groups = Firewall rules attached to each resource (EC2, RDS, etc.)
```

### Concept
Stateful firewalls controlling inbound/outbound traffic at the instance level. Default behavior: ALL inbound traffic is DENIED, ALL outbound is allowed.

### Student Analogy: College Hostel Security 🏫
```
┌─────────────────────────────────────────────────────────────────────┐
│                    HOSTEL SECURITY ANALOGY                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Your college hostel has security rules:                            │
│                                                                     │
│  INBOUND RULES (Who can come IN):                                   │
│  ├── Students with ID card → Allowed (Port 443 from known IPs)      │
│  ├── Parents with visitor pass → Allowed (specific IPs only)        │
│  ├── Food delivery (Swiggy) → Only to reception (Port 80)           │
│  └── Random strangers → BLOCKED (no 0.0.0.0/0 for SSH!)             │
│                                                                     │
│  OUTBOUND RULES (Who can go OUT):                                   │
│  └── Everyone inside can leave freely (default allow outbound)      │
│                                                                     │
│  STATEFUL BEHAVIOR:                                                 │
│  "If you ordered Swiggy (outbound request), the delivery guy        │
│   can enter with your food (return traffic auto-allowed)"           │
│                                                                     │
│  🔑 Security Group = Hostel security desk for your EC2 server       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Application Examples
| Scenario | Security Group Rule | Real-World Example |
|----------|---------------------|-------------------|
| **Public website** | Allow 80/443 from anywhere (0.0.0.0/0) | Flipkart homepage - anyone can browse |
| **Admin panel** | Allow 443 only from office IP (203.0.113.0/24) | Flipkart seller dashboard - only registered sellers |
| **Database** | Allow 3306 only from app-server security group | Customer data - only Flipkart app can access |
| **SSH access** | Allow 22 only from VPN (never 0.0.0.0/0!) | Server maintenance - only IT team via secure tunnel |
| **Game server** | Allow UDP 7777-7778 from players | PUBG/Valorant - game traffic only |

### Key Properties
- **Default:** All inbound denied, all outbound allowed
- **Stateful:** Return traffic automatically allowed
- **Instance-level:** Applied to ENI (Elastic Network Interface)
- **Allow rules only:** Cannot create deny rules

### Best Practices Architecture
```
┌─────────────────────────────────────────────────────┐
│                    INTERNET                         │
│                        │                            │
│                        ▼                            │
│  ┌──────────────────────────────────────────────┐   │
│  │        web-sg (Security Group)               │   │
│  │  Inbound: 80, 443 from 0.0.0.0/0             │   │
│  │  Inbound: 22 from 10.0.0.0/8 (VPN only)      │   │
│  │                                              │   │
│  │         [Web Servers - Public]               │   │
│  └──────────────────────────────────────────────┘   │
│                        │                            │
│                    Port 3306                        │
│                        ▼                            │
│  ┌──────────────────────────────────────────────┐   │
│  │        db-sg (Security Group)                │   │
│  │  Inbound: 3306 from web-sg ONLY              │   │
│  │  NO internet access                          │   │
│  │                                              │   │
│  │         [Database - Private]                 │   │
│  └──────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
```

> **WOW Moment: Open Port = Instant Attack**
> - Open port 3389 (RDP) to internet: Attacked within **15 MINUTES**
> - **600+ hack attempts PER DAY** per server with open ports
> - Bots scan EVERY IP address 24/7 looking for open ports
> - FIX: Allow only YOUR IP, never 0.0.0.0/0 for SSH/RDP

### Hardened Server Images (AMIs)

Pre-secured AMI templates with security configurations baked in.

```
┌─────────────────────────────────────────────────────────────────────┐
│              STANDARD vs HARDENED SERVER IMAGE                      │
├─────────────────────────────────┬───────────────────────────────────┤
│   DEFAULT AMAZON LINUX          │   HARDENED IMAGE (CIS Benchmark)  │
│   (Convenient but risky!)       │   (Secure by default)             │
├─────────────────────────────────┼───────────────────────────────────┤
│   ❌ SSH allows root login      │   ✅ Root login disabled          │
│   ❌ Password auth enabled      │   ✅ SSH key-only auth            │
│   ❌ Many services running      │   ✅ Only essential services      │
│   ❌ No firewall rules          │   ✅ Firewall configured          │
│   ❌ No auto-updates            │   ✅ Auto security updates        │
│   ❌ Default passwords          │   ✅ CIS Benchmark compliance     │
│   ❌ No audit logging           │   ✅ Full audit logging           │
├─────────────────────────────────┼───────────────────────────────────┤
│   Setup time: 2-4 hours/server  │   Setup time: 0 hours (pre-done!) │
└─────────────────────────────────┴───────────────────────────────────┘
```

**Where to Get Hardened AMIs:**
- AWS Marketplace - Search "CIS hardened" (some free, some paid)
- Create your own: Launch → Harden → Create AMI → Mandate for all servers
- AWS Systems Manager Patch Manager for automatic updates

### CLI Configuration

```bash
# Create web security group
aws ec2 create-security-group \
    --group-name web-sg \
    --description "Web server security group" \
    --vpc-id vpc-12345

# Allow HTTP/HTTPS from anywhere
aws ec2 authorize-security-group-ingress \
    --group-id sg-web123 \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0

aws ec2 authorize-security-group-ingress \
    --group-id sg-web123 \
    --protocol tcp \
    --port 443 \
    --cidr 0.0.0.0/0

# Create database security group
aws ec2 create-security-group \
    --group-name db-sg \
    --description "Database security group"

# Allow MySQL only from web security group (NOT IP!)
aws ec2 authorize-security-group-ingress \
    --group-id sg-db456 \
    --protocol tcp \
    --port 3306 \
    --source-group sg-web123
```

### Security Group vs NACL

| Feature | Security Group | Network ACL |
|---------|----------------|-------------|
| Level | Instance | Subnet |
| State | Stateful | Stateless |
| Rules | Allow only | Allow & Deny |
| Evaluation | All rules | Order-based |
| Default | Deny all inbound | Allow all |

---

### Self-Check Question #1

**Q: A developer accidentally adds a Security Group rule allowing SSH (port 22) from 0.0.0.0/0. What's the risk and how would you fix it?**

<details>
<summary>Click to see answer</summary>

**Risk:** Anyone on the internet can attempt to SSH into your server!
- Bots constantly scan the internet for open port 22
- Brute force attacks on passwords
- If they get in, full server access

**Fix:**
1. Remove the 0.0.0.0/0 rule immediately
2. Add rule allowing SSH only from your VPN IP (e.g., 10.0.0.0/8)
3. Better: Use AWS Systems Manager Session Manager (no port 22 needed!)
4. Enable CloudTrail to see who added the bad rule

</details>

---

## 2.3 IAM (Identity and Access Management)

### Interactive Question 🔔

```
┌──────────────────────────────────────────────────────────────────────┐
│  ❓ QUICK POLL (Raise your hand if you've ever...)                  │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  👋 Used the same password for multiple accounts?                    │
│  👋 Shared your Netflix/Hotstar password?                            │
│  👋 Given a friend your login "just for a minute"?                   │
│                                                                      │
│  😱 These are the EXACT behaviors that cause cloud breaches!         │
│     IAM exists to prevent this - let's learn how.                    │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
```

### Student Analogy: College ERP System 🎓
```
┌─────────────────────────────────────────────────────────────────────┐
│                    COLLEGE ERP SYSTEM ANALOGY                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  USERS (Individual accounts):                                       │
│  ├── Student (you@college.edu)                                      │
│  │   └── Can view: Own grades, attendance, fee status               │
│  │   └── Can't access: Other students' data, admin panel            │
│  │                                                                  │
│  ├── Professor (prof@college.edu)                                   │
│  │   └── Can view: Student list, upload grades                      │
│  │   └── Can't access: Fee collection, admit new students           │
│  │                                                                  │
│  └── Admin (admin@college.edu)                                      │
│      └── Can do: EVERYTHING (dangerous!)                            │
│                                                                     │
│  GROUPS (Collection of users):                                      │
│  ├── "CSE-2024" group → All CSE students, same permissions          │
│  ├── "Faculty" group → All professors, grade upload access          │
│  └── "Finance" group → Fee collection, scholarship management       │
│                                                                     │
│  ROLES (Temporary assumed identity):                                │
│  ├── "Exam-Controller" role → Prof assumes during exams only        │
│  ├── "Placement-Cell" role → Student assumes during interviews      │
│  └── Role expires after task done (unlike permanent user access)    │
│                                                                     │
│  POLICIES (Written rules):                                          │
│  └── "Students can view grades ONLY if logged in from campus IP"    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Application Examples
| Scenario | IAM Setup | Why This Matters |
|----------|----------|------------------|
| **Intern joins** | ReadOnly access to specific S3 bucket | Can't accidentally delete production data |
| **CI/CD pipeline** | Role with deploy permissions, no delete | Automated deploys can't destroy infrastructure |
| **Third-party audit** | Temporary role with read-only, expires in 24h | Auditor access auto-revokes, no cleanup needed |
| **Production DB** | Only DBA group has access, requires MFA | Even if password leaked, MFA stops attacker |
| **GitHub Actions** | OIDC federated role, no long-lived keys | No AWS credentials stored in GitHub secrets |

### Core Components

| Component | Purpose | Example |
|-----------|---------|---------|
| **User** | Individual identity | john@company.com |
| **Group** | Collection of users | Developers, Admins |
| **Role** | Assumed by services/users | EC2-S3-Role |
| **Policy** | JSON permission document | S3ReadOnlyAccess |

### IAM Policy Structure

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::my-bucket",
                "arn:aws:s3:::my-bucket/*"
            ],
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": "192.168.1.0/24"
                }
            }
        }
    ]
}
```

### IAM Best Practices

1. **Never use root account** - Create IAM admin user
2. **Enable MFA** - On all human users, especially privileged
3. **Least privilege** - Start with no permissions, add as needed
4. **Use groups** - Don't attach policies directly to users
5. **Use roles for services** - Never embed credentials in code
6. **Rotate credentials** - Access keys every 90 days
7. **Remove unused users** - Audit quarterly

---

### Self-Check Question #2

**Q: An intern hardcodes AWS access keys in a Python script and pushes it to a public GitHub repo. What happens next?**

<details>
<summary>Click to see answer</summary>

**What happens (real incidents!):**
1. Bots scan GitHub 24/7 for AWS keys
2. Within MINUTES, attacker uses your keys
3. They spin up 100s of expensive EC2 instances (crypto mining)
4. Your AWS bill: $50,000+ overnight!
5. AWS might suspend your account

**Prevention:**
1. Use IAM Roles instead of access keys (for EC2, Lambda)
2. Use AWS Secrets Manager or environment variables
3. Enable `git-secrets` to block commits with keys
4. Set up billing alerts ($10 threshold!)
5. Immediately rotate compromised keys via IAM console

**Real case:** A student's leaked key resulted in $140,000 AWS bill in 2023.

</details>

---

### CLI Commands

```bash
# Create user
aws iam create-user --user-name developer1

# Create group and add user
aws iam create-group --group-name Developers
aws iam add-user-to-group --user-name developer1 --group-name Developers

# Attach managed policy to group
aws iam attach-group-policy \
    --group-name Developers \
    --policy-arn arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess

# Create role for EC2
aws iam create-role \
    --role-name EC2-S3-Access \
    --assume-role-policy-document file://trust-policy.json

# Enable MFA
aws iam enable-mfa-device \
    --user-name developer1 \
    --serial-number arn:aws:iam::123456789:mfa/developer1 \
    --authentication-code1 123456 \
    --authentication-code2 789012
```

### Terraform IAM Example

```hcl
# Create IAM role for EC2
resource "aws_iam_role" "ec2_s3_role" {
  name = "ec2-s3-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Action = "sts:AssumeRole"
      Effect = "Allow"
      Principal = {
        Service = "ec2.amazonaws.com"
      }
    }]
  })
}

# Attach S3 read-only policy
resource "aws_iam_role_policy_attachment" "s3_read" {
  role       = aws_iam_role.ec2_s3_role.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess"
}

# Create instance profile
resource "aws_iam_instance_profile" "ec2_profile" {
  name = "ec2-profile"
  role = aws_iam_role.ec2_s3_role.name
}
```

---

## 2.4 Data Security Mechanisms

### Student Analogy: Sending Exam Papers 📝
```
┌─────────────────────────────────────────────────────────────────────┐
│                    EXAM PAPER SECURITY ANALOGY                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  DATA AT REST (Storage):                                            │
│  ├── Question papers stored in LOCKED CUPBOARD (encrypted S3)       │
│  ├── Only HOD has the key (KMS key management)                      │
│  └── Even if someone breaks into office, papers are useless         │
│                                                                     │
│  DATA IN TRANSIT (Transfer):                                        │
│  ├── Papers sent in SEALED ENVELOPE to exam center (TLS/HTTPS)      │
│  ├── Tamper-evident seal shows if opened (certificate validation)   │
│  └── Even if postman is corrupt, can't read contents                │
│                                                                     │
│  DATA IN USE (Processing):                                          │
│  ├── Papers opened ONLY in exam hall (secure enclave)               │
│  ├── No phones allowed (isolated processing environment)            │
│  └── Invigilator supervision (access logging/CloudTrail)            │
│                                                                     │
│  🔐 Without encryption: Anyone with server access can read data!    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Application Examples
| Data Type | Protection Method | Real-World Service |
|-----------|------------------|--------------------|
| **Aadhaar data** | Encrypted at rest (SSE-KMS) + in transit (TLS) | UIDAI servers |
| **Payment info** | PCI-DSS compliance requires encryption everywhere | Razorpay, PayTM |
| **Medical records** | HIPAA requires encryption + access logging | Practo, 1mg |
| **Source code** | Encrypted S3 + IAM restrictions | GitHub private repos |
| **Chat messages** | End-to-end encryption | WhatsApp, Signal |
| **Streaming content** | DRM + encrypted delivery | Netflix, Hotstar |

### Data States and Protection

| State | Description | Protection |
|-------|-------------|------------|
| At Rest | Stored in S3, EBS, RDS | AES-256 encryption |
| In Transit | Network transfer | TLS 1.2+ |
| In Use | Processing in memory | Nitro Enclaves |

### S3 Encryption Options

| Type | Key Management | Use Case |
|------|----------------|----------|
| SSE-S3 | AWS managed | Default, simple |
| SSE-KMS | Customer managed via KMS | Audit trail, key rotation |
| SSE-C | Customer provided | Full control |
| Client-side | Encrypt before upload | Maximum security |

### Enable S3 Encryption

```bash
# Enable default encryption on bucket
aws s3api put-bucket-encryption \
    --bucket my-bucket \
    --server-side-encryption-configuration '{
        "Rules": [{
            "ApplyServerSideEncryptionByDefault": {
                "SSEAlgorithm": "aws:kms",
                "KMSMasterKeyID": "alias/my-key"
            }
        }]
    }'

# Block public access
aws s3api put-public-access-block \
    --bucket my-bucket \
    --public-access-block-configuration '{
        "BlockPublicAcls": true,
        "IgnorePublicAcls": true,
        "BlockPublicPolicy": true,
        "RestrictPublicBuckets": true
    }'
```

### Data Loss Prevention (DLP)

**Amazon Macie** - ML-powered sensitive data discovery

```bash
# Enable Macie
aws macie2 enable-macie

# Create classification job
aws macie2 create-classification-job \
    --job-type ONE_TIME \
    --s3-job-definition '{
        "bucketDefinitions": [{
            "accountId": "123456789012",
            "buckets": ["my-sensitive-bucket"]
        }]
    }' \
    --name "PII-Scan"
```

**Detected patterns:**
- Credit card numbers
- Social Security Numbers
- API keys and secrets
- Personal health information (PHI)

### Three States of Data - Visual
```
┌─────────────────────────────────────────────────────────────────────┐
│                    THE THREE STATES OF DATA                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. DATA AT REST 💾                                                │
│  ─────────────────                                                  │
│  • Stored in databases, S3 buckets, hard drives                     │
│  • Protection: AES-256 encryption                                   │
│  • AWS Services: S3 encryption, RDS encryption, EBS encryption      │
│                                                                     │
│   ┌───────────┐    ┌───────────┐    ┌───────────┐                   │
│   │ 🔒 S3    │    │ 🔒 RDS   │    │ 🔒 EBS   │                   │
│   │ Encrypted │    │ Encrypted │    │ Encrypted │                   │
│   └───────────┘    └───────────┘    └───────────┘                   │
│                                                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  2. DATA IN MOTION 🚀                                              │
│  ────────────────────                                               │
│  • Being transmitted over network, API calls, emails                │
│  • Protection: TLS 1.2+ / HTTPS                                     │
│  • AWS Services: Certificate Manager, HTTPS on ALB                  │
│                                                                     │
│   CLIENT          🔒 HTTPS/TLS 🔒           SERVER                  │
│   ┌──────┐     ═══════════════════════▶    ┌──────┐                │
│   │  👤  │     Encrypted in transit        │  🖥️  │                │
│   └──────┘     ◀═══════════════════════    └──────┘                │
│                                                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  3. DATA IN USE 🔄                                                 │
│  ──────────────────                                                 │
│  • Being processed, displayed on screens, in memory                 │
│  • Protection: Access controls, DLP monitoring, enclaves            │
│  • AWS Services: IAM, CloudWatch, Macie, Nitro Enclaves             │
│                                                                     │
│   Application processing sensitive data:                            │
│   ┌────────────────────────────────────┐                            │
│   │   Name: John Smith                 │  DLP monitors              │
│   │   SSN: 123-45-6789  ← ⚠️ PII!     │  screen captures,          │
│   │   CC: 4111-1111...  ← ⚠️ PCI!     │  copy/paste, print         │
│   └────────────────────────────────────┘                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### DLP in Action - Real Scenarios
```
┌─────────────────────────────────────────────────────────────────────┐
│                    DLP IN ACTION - SCENARIO                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  SCENARIO: Employee uploads sensitive file to public cloud          │
│                                                                     │
│  👤 Employee uploads to personal Dropbox:                           │
│  ┌──────────────────────────┐                                       │
│  │ customer_creditcards.csv │ ──▶ Public Dropbox folder             │
│  └──────────────────────────┘                                       │
│                              │                                      │
│                              ▼                                      │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │                    DLP ENGINE (Macie)                       │    │
│  │                                                             │    │
│  │   ⚠️  DETECTED: Credit card numbers (16 digits)             │    │
│  │   ⚠️  DESTINATION: Unapproved cloud storage                 │    │
│  │                                                             │    │
│  │   ACTIONS:                                                  │    │
│  │   1. ❌ BLOCKED upload                                      │    │
│  │   2. 📧 Alert to IT Security                                │    │
│  │   3. 📁 Auto-move file to approved encrypted storage        │    │
│  │   4. 📝 Log incident for audit                              │    │
│  │                                                             │    │
│  └─────────────────────────────────────────────────────────────┘    │
│                                                                     │
│  Macie Scan Results Example:                                        │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │  S3 Bucket: "company-data"                                  │    │
│  │  ⚠️  customers.csv     - Contains 1,234 SSNs                │    │
│  │  ⚠️  payments.xlsx     - Contains 567 Credit Card numbers   │    │
│  │  ⚠️  employees.json    - Contains PII (names, addresses)    │    │
│  │  ✅  images/           - No sensitive data detected         │    │
│  │  ✅  logs/             - No sensitive data detected         │    │
│  └─────────────────────────────────────────────────────────────┘    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

> **WOW Moment: Data Breach Reality**
> - **Twitch (2021):** 125GB source code leaked - no encryption on internal repos
> - **Facebook (2021):** 533 million phone numbers exposed - old unencrypted backup
> - **LinkedIn (2021):** 700 million user records scraped - API not rate-limited
> - Most breaches are from MISCONFIGURATIONS, not sophisticated hacks!

---

## 📝 Security Architecture Quiz: Identify the Vulnerabilities!

**Before moving to monitoring, test your understanding. Look at this architecture and spot the security issues:**

```
    QUESTION: What's WRONG with this setup?
    
                            INTERNET
                               │
                               ▼
                        ┌─────────────┐
                        │  EC2 (web)  │  ← Security Group: SSH from 0.0.0.0/0
                        │  IAM Role:  │    HTTP/HTTPS from 0.0.0.0/0
                        │  Admin      │
                        └──────┬──────┘
                               │
                               │ Port 3306 open to 0.0.0.0/0
                               ▼
                        ┌─────────────┐
                        │    RDS      │  ← No encryption enabled
                        │  (MySQL)    │    Public subnet
                        │             │    Password: admin123
                        └─────────────┘
                               │
                               ▼
                        ┌─────────────┐
                        │     S3      │  ← Bucket policy: Public read
                        │  (backups)  │    No encryption
                        │             │    Contains customer PII
                        └─────────────┘
```

<details>
<summary>Click to see all vulnerabilities</summary>

| Issue | Risk | Fix |
|-------|------|-----|
| **SSH from 0.0.0.0/0** | Brute force attacks | Allow SSH only from VPN/bastion |
| **IAM Admin role on EC2** | Full account compromise if hacked | Least privilege - only needed permissions |
| **MySQL port 3306 public** | Direct database attacks | Allow only from web-sg security group |
| **RDS in public subnet** | Exposed to internet | Move to private subnet |
| **RDS no encryption** | Data readable if disk stolen | Enable encryption at rest |
| **Weak DB password** | Easy to crack | Use Secrets Manager, 20+ char password |
| **S3 public read** | Anyone can download backups | Block public access, use IAM policies |
| **S3 no encryption** | Data exposed if breached | Enable SSE-KMS encryption |
| **PII in S3** | Compliance violation (GDPR, etc.) | Classify data, enable Macie scanning |
| **No CloudTrail** | Can't detect attacks | Enable multi-region CloudTrail |

**This is exactly how Capital One was breached!**

</details>

---

## 2.5 Security Monitoring & Governance

### Student Analogy: Campus Security System 📹
```
┌─────────────────────────────────────────────────────────────────────┐
│                    CAMPUS SECURITY ANALOGY                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  CloudTrail = CCTV Footage Recording                                │
│  ├── Records WHO entered WHICH building at WHAT time                │
│  ├── "Student A swiped into Lab at 2:30 AM" (API call logged)       │
│  ├── Can review footage later to investigate incidents              │
│  └── Doesn't STOP bad actions, just RECORDS them                    │
│                                                                     │
│  GuardDuty = Smart AI Security Guard                                │
│  ├── Watches all CCTV feeds in real-time                            │
│  ├── Notices: "Someone's trying 100 doors at 3 AM" (brute force)    │
│  ├── Alerts: "Unknown person following students" (suspicious IP)    │
│  └── Uses ML to detect unusual patterns you'd miss                  │
│                                                                     │
│  AWS Config = Building Code Inspector                               │
│  ├── Checks: "Is fire exit blocked?" (is S3 bucket public?)         │
│  ├── Reports: "Room 203 violates safety code" (non-compliant)       │
│  └── Continuous compliance monitoring                               │
│                                                                     │
│  Together = DETECT + RECORD + PREVENT                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### CloudTrail - Audit Logging

Records ALL API calls in your AWS account. Think of it as CCTV for your cloud.

**What gets logged:**
| Action | CloudTrail Records |
|--------|-------------------|
| Someone creates EC2 | Who, when, what size, which region |
| Someone deletes S3 bucket | Who, when, which bucket, from where (IP) |
| Failed login attempt | Who tried, when, why it failed |
| IAM policy change | Who changed what, before/after values |

```bash
# Create trail
aws cloudtrail create-trail \
    --name my-trail \
    --s3-bucket-name my-logs-bucket \
    --is-multi-region-trail

# Start logging
aws cloudtrail start-logging --name my-trail

# Query events
aws cloudtrail lookup-events \
    --lookup-attributes AttributeKey=EventName,AttributeValue=DeleteBucket
```

### GuardDuty - Threat Detection

AI-powered threat detection that analyzes:
- CloudTrail events (API calls)
- VPC Flow Logs (network traffic)
- DNS logs (domain lookups)
- EKS audit logs (Kubernetes activity)

**Real-World Finding Examples:**
| Finding | What It Means | Real Scenario |
|---------|--------------|---------------|
| `UnauthorizedAccess:EC2/TorClient` | EC2 talking to Tor network | Attacker hiding tracks |
| `CryptoCurrency:EC2/BitcoinTool` | Crypto mining detected | Your bill about to explode! |
| `Recon:EC2/PortProbeUnprotectedPort` | Someone scanning your ports | Attacker mapping your infra |
| `Trojan:EC2/DNSDataExfiltration` | Data being stolen via DNS | Capital One style attack |
| `Impact:EC2/WinRMBruteForce` | Windows password guessing | Bot trying to break in |

```bash
# Enable GuardDuty (one-click security!)
aws guardduty create-detector --enable

# List findings
aws guardduty list-findings --detector-id abc123

# Get finding details
aws guardduty get-findings --detector-id abc123 --finding-ids finding-id-1
```

> **WOW Moment: GuardDuty Saves $48,000**
> ```
> Real incident - AWS keys leaked on GitHub:
> • 15 min later: Hackers spun up 100 GPU instances for crypto mining
> • Potential bill: $50,000+ overnight!
> • GuardDuty alerted the company in 10 minutes
> • Actual damage: $2,000 (saved $48,000!)
> 
> Why it works: GuardDuty's ML is trained on 10+ years of AWS threat data,
> analyzing trillions of events daily. Cost: ~$4/month (free trial available)
> ```

### AWS Config - Compliance

Continuous compliance monitoring with rules. Like having an automated auditor.

**Common rules:**
| Rule | What It Checks | Why It Matters |
|------|---------------|----------------|
| `s3-bucket-public-read-prohibited` | Is any S3 bucket public? | Prevents data leaks |
| `ec2-instance-no-public-ip` | Does EC2 have public IP? | Forces traffic through LB |
| `iam-password-policy` | Is password policy strong? | Prevents weak passwords |
| `rds-storage-encrypted` | Is database encrypted? | Compliance requirement |
| `vpc-flow-logs-enabled` | Are network logs on? | Required for forensics |

```bash
# Enable Config
aws configservice put-configuration-recorder \
    --configuration-recorder name=default,roleARN=arn:aws:iam::123456789:role/ConfigRole

# Add managed rule
aws configservice put-config-rule \
    --config-rule '{
        "ConfigRuleName": "s3-bucket-public-read-prohibited",
        "Source": {
            "Owner": "AWS",
            "SourceIdentifier": "S3_BUCKET_PUBLIC_READ_PROHIBITED"
        }
    }'
```

---

## 2.6 Lab Exercise: Secure AWS Environment

### Lab Overview

| Task | Time | What You'll Do |
|------|------|----------------|
| 1. Secure Root Account | 5 min | Enable MFA, create admin user |
| 2. Create VPC Architecture | 10 min | Public + private subnets |
| 3. Configure Security Groups | 10 min | Web, app, and DB tiers |
| 4. Enable Monitoring | 10 min | CloudTrail, GuardDuty, Config |
| 5. Set Up Billing Alert | 5 min | $10 threshold notification |
| 6. Verify & Cleanup | 5 min | Test access, terminate resources |

### Objective
Implement security best practices on an AWS account.

### Task 1: Secure Root Account

**In AWS Console:**
1. Click your account name (top right) → **Security credentials**
2. Under **Multi-factor authentication (MFA)** → **Assign MFA device**
3. Choose **Authenticator app** → scan QR with Google Authenticator
4. Enter two consecutive codes → **Add MFA**

**Then create admin user:**
```bash
# Or via CLI after console setup:
aws iam create-user --user-name admin
aws iam attach-user-policy \
    --user-name admin \
    --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
# Enable MFA via console for the admin user too!
```

> ⚠️ **IMPORTANT:** After this, STOP using root account. Use your new admin user!

### Task 2: Create Secure VPC Architecture

**In AWS Console: VPC → Create VPC**

| Setting | Value |
|---------|-------|
| Name | `secure-vpc` |
| IPv4 CIDR | `10.0.0.0/16` |
| Tenancy | Default |

**Create Subnets:**

| Subnet | CIDR | AZ | Purpose |
|--------|------|-----|--------|
| `public-subnet` | `10.0.1.0/24` | us-east-1a | Web servers |
| `private-subnet` | `10.0.2.0/24` | us-east-1a | Databases |

```bash
# Via CLI:
aws ec2 create-vpc --cidr-block 10.0.0.0/16 --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=secure-vpc}]'

# Note the VPC ID from output, then:
aws ec2 create-subnet --vpc-id vpc-xxx --cidr-block 10.0.1.0/24 --availability-zone us-east-1a
aws ec2 create-subnet --vpc-id vpc-xxx --cidr-block 10.0.2.0/24 --availability-zone us-east-1a
```

### Task 3: Configure Security Groups

**Create three security groups:**

| Security Group | Inbound Rules | Purpose |
|----------------|---------------|--------|
| `web-sg` | 80, 443 from 0.0.0.0/0; 22 from YOUR IP only | Web servers |
| `app-sg` | 8080 from web-sg only | Application tier |
| `db-sg` | 3306 from app-sg only | Database tier |

**In Console: EC2 → Security Groups → Create security group**

```bash
# Create web security group
aws ec2 create-security-group \
    --group-name web-sg \
    --description "Web tier - public access" \
    --vpc-id vpc-xxx

# Allow HTTP/HTTPS from anywhere
aws ec2 authorize-security-group-ingress \
    --group-id sg-web \
    --protocol tcp --port 80 --cidr 0.0.0.0/0

aws ec2 authorize-security-group-ingress \
    --group-id sg-web \
    --protocol tcp --port 443 --cidr 0.0.0.0/0

# Allow SSH only from your IP (replace with your actual IP)
aws ec2 authorize-security-group-ingress \
    --group-id sg-web \
    --protocol tcp --port 22 --cidr $(curl -s ifconfig.me)/32
```

### Task 4: Enable Monitoring

**Enable CloudTrail:**
1. Search "CloudTrail" → **Create trail**
2. Name: `security-audit-trail`
3. Apply to all regions: **Yes**
4. Create new S3 bucket for logs

**Enable GuardDuty:**
1. Search "GuardDuty" → **Get Started** → **Enable GuardDuty**
2. That's it! GuardDuty starts analyzing immediately.

```bash
# Via CLI:
aws cloudtrail create-trail \
    --name security-audit-trail \
    --s3-bucket-name my-cloudtrail-logs-$(date +%s) \
    --is-multi-region-trail

aws cloudtrail start-logging --name security-audit-trail

aws guardduty create-detector --enable
```

### Task 5: Set Up Billing Alert

**Critical for students!** Avoid surprise bills.

1. Search "Billing" → **Budgets** → **Create budget**
2. Choose **Cost budget**
3. Budget amount: `$10`
4. Email: your email address

```bash
# Create billing alarm (requires us-east-1)
aws cloudwatch put-metric-alarm \
    --alarm-name "BillingAlert-10USD" \
    --metric-name EstimatedCharges \
    --namespace AWS/Billing \
    --statistic Maximum \
    --period 86400 \
    --threshold 10 \
    --comparison-operator GreaterThanThreshold \
    --evaluation-periods 1 \
    --alarm-actions arn:aws:sns:us-east-1:YOUR_ACCOUNT:billing-alerts \
    --dimensions Name=Currency,Value=USD
```

### Task 6: Verify & Cleanup

**Verification Checklist:**
- [ ] Can you login with admin user (not root)?
- [ ] Is MFA enabled on both root and admin?
- [ ] Do Security Groups show correct rules?
- [ ] Is CloudTrail logging events?
- [ ] Is GuardDuty enabled and showing status?

**Cleanup (to avoid charges):**
```bash
# Delete resources you created:
aws ec2 delete-security-group --group-id sg-xxx
aws ec2 delete-subnet --subnet-id subnet-xxx
aws ec2 delete-vpc --vpc-id vpc-xxx
# Keep CloudTrail and GuardDuty for learning (minimal cost)
```

---

## 2.7 Serverless Computing with Lambda

### Concept
Run code without provisioning servers. Pay only when code executes (~$0.0000002 per request). AWS manages all infrastructure - you just write code.

### Student Analogy: College Canteen vs Food Delivery 🍔
```
┌─────────────────────────────────────────────────────────────────────┐
│                    COMPUTE MODELS COMPARISON                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  EC2 (Virtual Server) = Running your own canteen                    │
│  ├── Pay rent 24/7 (₹30,000/month) even if no customers             │
│  ├── Hire cook, buy equipment, manage everything                    │
│  ├── Can serve 100 people/hour (fixed capacity)                     │
│  └── If 500 people come → Long queues! (need to scale manually)     │
│                                                                     │
│  Lambda (Serverless) = Ordering from Swiggy/Zomato                  │
│  ├── Pay ₹0 when no orders (no fixed cost!)                         │
│  ├── Swiggy handles cooking, delivery (AWS manages servers)         │
│  ├── Can serve 1 or 10,000 orders (auto-scales!)                    │
│  └── Pay only: ₹5 per order × number of orders                      │
│                                                                     │
│  💡 WHEN TO USE WHAT:                                               │
│  ├── EC2: Predictable load, need full control (gaming server)       │
│  └── Lambda: Unpredictable/spiky load (OTP service, webhooks)       │
│                                                                     │
│  Cost Example (100 requests/day):                                   │
│  ├── EC2 t2.micro: $8.50/month (always running)                     │
│  └── Lambda: $0.0003/month (3000x cheaper!)                         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Application Examples
| Use Case | Lambda Implementation | Companies Using This |
|----------|----------------------|----------------------|
| **Image resize** | S3 upload triggers Lambda → creates thumbnail | Instagram, Pinterest |
| **OTP generation** | API Gateway → Lambda → sends SMS | All banking apps |
| **Slack/Discord bot** | Webhook → Lambda → responds to commands | Thousands of bots |
| **Scheduled reports** | CloudWatch cron → Lambda → emails PDF | Business dashboards |
| **IoT processing** | Device data → Lambda → stores in DynamoDB | Smart home devices |
| **Payment webhooks** | Razorpay/Stripe → Lambda → updates order | E-commerce sites |
| **AI inference** | API request → Lambda → runs ML model | ChatGPT API wrappers |

### Lambda Function Example

```python
import json
from datetime import datetime

def lambda_handler(event, context):
    return {
        'statusCode': 200,
        'body': json.dumps({
            'message': 'Hello from Lambda!',
            'timestamp': datetime.now().isoformat()
        })
    }
```

### Create Lambda via CLI

```bash
# Create function
aws lambda create-function \
    --function-name hello-cloud \
    --runtime python3.12 \
    --role arn:aws:iam::123456789:role/lambda-role \
    --handler lambda_function.lambda_handler \
    --zip-file fileb://function.zip

# Create Function URL (public API)
aws lambda create-function-url-config \
    --function-name hello-cloud \
    --auth-type NONE

# Security Note: auth-type NONE means anyone with URL can invoke.
# Use AWS_IAM for production or add authentication in code.

# Invoke function
aws lambda invoke \
    --function-name hello-cloud \
    --payload '{}' \
    response.json
```

### Lambda Limits
| Resource | Limit |
|----------|-------|
| Execution timeout | 15 minutes max |
| Memory | 128 MB - 10 GB |
| Package size | 50 MB zipped, 250 MB unzipped |
| Concurrent executions | 1000 default (can increase) |

---

### Self-Check Question #3

**Q: You're building an OTP service that sends 1000 SMS/day. Should you use EC2 or Lambda?**

<details>
<summary>Click to see answer</summary>

**Answer: Lambda** 

**Why:**
| Factor | EC2 | Lambda |
|--------|-----|--------|
| Cost for 1000 requests/day | ~$8.50/month (t2.micro running 24/7) | ~$0.0003/month |
| Scaling | Manual - need to add instances | Automatic - handles 1 to 1 million |
| Maintenance | You patch OS, manage security | AWS handles everything |
| Availability | 99.5% (single instance) | 99.95% (built-in) |

**When EC2 IS better:**
- Need persistent connections (WebSockets, gaming)
- Execution longer than 15 minutes
- Need full OS control
- Predictable high load (cheaper at scale)

</details>

---

> **WOW Moment: Serverless Success Stories**
> - **Coca-Cola** runs 400+ Lambda functions, cut costs by 65%
> - **Netflix** processes 700 BILLION events/day with Lambda
> - **iRobot (Roomba)** handles 30+ million robots via serverless
> - **Bustle** went from $30,000/month to $2,500/month with Lambda

---

## 2.8 Case Study: MediCare Health Insurance Cloud Migration

### The Scenario
```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│   COMPANY PROFILE: MediCare Health Insurance                        │
│   ─────────────────────────────────────────                         │
│   • Mid-sized health insurance company                              │
│   • 5,000 employees across 20 offices                               │
│   • Must comply with HIPAA (health data protection law)             │
│   • Current: Legacy data center that's aging                        │
│   • Goal: Migrate claims processing system to AWS                   │
│                                                                     │
│   SENSITIVE DATA TYPES:                                             │
│   • Patient names, addresses, SSNs                                  │
│   • Medical records and diagnoses                                   │
│   • Insurance claim details                                         │
│   • Payment information                                             │
│                                                                     │
│   IF THIS DATA LEAKS:                                               │
│   • $100-$50,000 per record in HIPAA fines                          │
│   • Criminal charges possible                                       │
│   • Lawsuits from affected patients                                 │
│   • Reputation destruction                                          │
│                                                                     │
│   QUESTION: "How do we migrate safely? Who is responsible for what?"│
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Discussion: Who Is Responsible for What?

**Think about this before looking at the answer:**

| Group A: AWS Responsibilities | Group B: MediCare Responsibilities |
|-------------------------------|------------------------------------|
| Physical security             | Data protection                    |
| Hardware                      | Access control (IAM)               |
| Network infrastructure        | Application security               |
| Hypervisor                    | Employee training                  |
| Compliance certifications     | OS patching (EC2)                  |

<details>
<summary>Click to see detailed breakdown</summary>

**AWS Responsibility ("Security OF the Cloud"):**
- Physical data center security (guards, biometrics)
- Hardware maintenance and replacement
- Network infrastructure
- Hypervisor security and patching
- Compliance certifications (SOC 1/2/3, HIPAA eligible)

**MediCare Responsibility ("Security IN the Cloud"):**
- Data classification and encryption
- IAM policies (who can access what)
- MFA enforcement for all users
- Application code security
- EC2 OS patching
- Security Group rules
- Employee security training
- Incident response procedures

</details>

### Key Lesson

> 💡 **"The cloud is secure. Your configuration might not be."**
>
> Remember the Capital One breach from Section 2.1? AWS infrastructure was perfect - the breach happened because of customer misconfigurations. MediCare must ensure they configure IAM, Security Groups, and encryption correctly.

---

## Summary: Key Takeaways

### Session 2 Learning Outcomes

After completing this session, you should be able to:

| # | Outcome | Verified By |
|---|---------|-------------|
| 1 | Explain the Shared Responsibility Model | Quiz question |
| 2 | Configure Security Groups for tiered architecture | Lab exercise |
| 3 | Create IAM users, groups, roles with least privilege | Lab exercise |
| 4 | Enable encryption for S3 and RDS | Lab exercise |
| 5 | Set up CloudTrail for audit logging | Lab exercise |
| 6 | Enable GuardDuty for threat detection | Lab exercise |
| 7 | Deploy a Lambda function with Function URL | Lab exercise |
| 8 | Identify security misconfigurations in architectures | Quiz |

### Why Security Skills Matter for Your Career

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SECURITY CAREER IMPACT                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  📈 SALARY BOOST:                                                   │
│     • Security-focused roles pay 15-25% more                        │
│     • AWS Security Specialty cert = avg $30K higher salary          │
│     • CISO (Chief Info Security Officer) = $200-400K/year           │
│                                                                     │
│  💼 JOB DEMAND:                                                     │
│     • 3.5 million cybersecurity jobs unfilled globally (2025)       │
│     • Security skills required for ALL cloud roles                  │
│     • DevSecOps is the hottest job market                           │
│                                                                     │
│  🎯 ROLES THAT NEED THIS:                                           │
│     • Cloud Security Engineer    • DevSecOps Engineer               │
│     • Security Architect         • Penetration Tester               │
│     • Compliance Analyst         • SOC Analyst                      │
│                                                                     │
│  "Every developer MUST understand security. It's not optional."     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Security Mechanisms Summary

| Mechanism | AWS Service | When to Use |
|-----------|-------------|-------------|
| Virtual Firewall | Security Groups | Every EC2, RDS instance |
| Identity Management | IAM | User access, service permissions |
| Encryption at Rest | KMS, SSE | All sensitive data storage |
| Encryption in Transit | ACM, TLS | All API calls, web traffic |
| Audit Logging | CloudTrail | Always (free 90-day retention) |
| Threat Detection | GuardDuty | Always (small monthly cost) |
| Compliance Monitoring | AWS Config | Regulated industries |
| Data Discovery | Amazon Macie | S3 buckets with PII |
| DDoS Protection | AWS Shield | Public-facing applications |
| Web Application Firewall | AWS WAF | Web apps exposed to internet |

### Cloud Management Mechanisms

| Mechanism | AWS Service | Use When |
|-----------|-------------|----------|
| Load Balancing | ELB (ALB/NLB) | High traffic, HA required |
| Auto Scaling | Auto Scaling Groups | Variable workloads |
| Multi-Cloud | Terraform, K8s | Vendor independence |
| Cloud Balancing | Route 53 | Global users |
| Edge Computing | CloudFront, Lambda@Edge | Low latency needed |
| Fog Computing | IoT Greengrass | IoT aggregation |
| Static Hosting | S3 Website | Simple sites, SPAs |
| Serverless | Lambda | Event-driven, APIs |

### Security Best Practices Checklist

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SECURITY CHECKLIST                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  IDENTITY:                                                          │
│  ☐ Enable MFA on root account                                       │
│  ☐ Enable MFA on all IAM users                                      │
│  ☐ Use roles instead of access keys                                 │
│  ☐ Apply least privilege (no AdministratorAccess!)                  │
│  ☐ Review IAM access quarterly                                      │
│                                                                     │
│  NETWORK:                                                           │
│  ☐ No SSH (22) from 0.0.0.0/0                                       │
│  ☐ No RDP (3389) from 0.0.0.0/0                                     │
│  ☐ Database in private subnet only                                  │
│  ☐ Use security group references (not IPs)                          │
│                                                                     │
│  DATA:                                                              │
│  ☐ Enable S3 default encryption                                     │
│  ☐ Block S3 public access                                           │
│  ☐ Enable RDS encryption                                            │
│  ☐ Use TLS 1.2+ for all connections                                 │
│                                                                     │
│  MONITORING:                                                        │
│  ☐ Enable CloudTrail (all regions)                                  │
│  ☐ Enable GuardDuty                                                 │
│  ☐ Set billing alert ($10 threshold)                                │
│  ☐ Enable VPC Flow Logs                                             │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### AWS Free Tier Limits
- EC2: 750 hours/month t2.micro
- S3: 5GB storage
- RDS: 750 hours/month db.t2.micro
- Lambda: 1M requests/month

### Certification Path
1. **Cloud Practitioner** (foundational) - 20 hrs study
2. **Solutions Architect Associate** (most valuable) - 60 hrs study
3. **Security Specialty** or **DevOps Professional** - 80+ hrs study

---

## Hands-on Resources

### Worksheets (GUI-based labs for students)

| Worksheet | Duration | Topics Covered |
|-----------|----------|----------------|
| **Session 1** | 35 min | EC2 launch, Security Groups, Apache install, S3 Static Website |
| **Session 2** | 60 min | IAM + MFA, Security Groups (tiered), S3 encryption, Lambda serverless, CloudTrail, GuardDuty, VPC exploration |

---

## What's Next?

### Apply What You Learned
```
┌─────────────────────────────────────────────────────────────────────┐
│                    IMMEDIATE ACTION ITEMS                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  TODAY (Before you forget!):                                        │
│  ☐ Enable MFA on your AWS root account                              │
│  ☐ Create an IAM admin user (stop using root!)                      │
│  ☐ Set up a $10 billing alert                                       │
│                                                                     │
│  THIS WEEK:                                                         │
│  ☐ Complete the hands-on lab worksheet                              │
│  ☐ Enable GuardDuty on your account (30-day free trial)             │
│  ☐ Review Security Groups on any existing EC2 instances             │
│                                                                     │
│  FOR YOUR PORTFOLIO:                                                │
│  ☐ Document a "secure architecture" diagram                         │
│  ☐ Write a blog post about Shared Responsibility Model              │
│  ☐ Get AWS Cloud Practitioner certified (validates these skills)    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Further Learning Resources
| Resource | What You'll Learn | Time |
|----------|------------------|------|
| AWS Security Fundamentals (Free) | Deep dive into all security services | 4 hours |
| AWS Well-Architected Labs | Hands-on security scenarios | 2-3 hours |
| OWASP Top 10 | Web application security | 2 hours |
| CIS AWS Benchmarks | Industry security standards | Reference |

---

## References

- [AWS Well-Architected Framework - Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/)
- [AWS Security Best Practices](https://docs.aws.amazon.com/security/)
- [CIS AWS Foundations Benchmark](https://www.cisecurity.org/benchmark/amazon_web_services)
- [OWASP Cloud Security](https://owasp.org/www-project-cloud-security/)
- [Capital One Breach Analysis](https://krebsonsecurity.com/2019/07/capital-one-data-theft-impacts-106m-people/)

---

## Final Quiz: Test Your Knowledge

**Answer these without looking back at the material!**

### Question 1: Shared Responsibility
A company's S3 bucket is accidentally set to public, exposing customer data. Who is responsible?

a) AWS - they should have blocked it  
b) The customer - they configured it wrong  
c) Both equally  
d) Neither - it's a bug  

<details>
<summary>Answer</summary>

**b) The customer**

AWS provides the tools (bucket policies, Block Public Access), but the customer must configure them correctly. This is "Security IN the Cloud" - customer responsibility.

</details>

---

### Question 2: Security Groups
Which Security Group rule is MOST dangerous?

a) Inbound HTTP (80) from 0.0.0.0/0  
b) Inbound SSH (22) from 0.0.0.0/0  
c) Inbound HTTPS (443) from 0.0.0.0/0  
d) Outbound all traffic to 0.0.0.0/0  

<details>
<summary>Answer</summary>

**b) Inbound SSH (22) from 0.0.0.0/0**

SSH gives direct command-line access to your server. Allowing it from anywhere means anyone on the internet can try to brute-force your credentials. Bots attack such servers within 15 minutes!

</details>

---

### Question 3: IAM Best Practice
Which is the BEST approach for giving an EC2 instance access to S3?

a) Create an IAM user and embed access keys in the code  
b) Create an IAM role and attach it to the EC2 instance  
c) Use the root account credentials  
d) Share one developer's access keys across all instances  

<details>
<summary>Answer</summary>

**b) Create an IAM role and attach it to the EC2 instance**

IAM Roles are automatically rotated, don't need hardcoded credentials, and follow the principle of least privilege. Never embed access keys in code (they get leaked to GitHub!).

</details>

---

### Question 4: Encryption
Data that is stored on a hard drive is called:

a) Data in Transit  
b) Data at Rest  
c) Data in Use  
d) Data in Motion  

<details>
<summary>Answer</summary>

**b) Data at Rest**

- **At Rest** = stored on disk (S3, EBS, RDS)
- **In Transit** = moving over network (HTTPS, TLS)
- **In Use** = being processed in memory

</details>

---

### Question 5: Threat Agents
Which threat agent is typically the MOST dangerous?

a) External hackers  
b) Malicious insiders  
c) Automated bots  
d) Anonymous attackers  

<details>
<summary>Answer</summary>

**b) Malicious insiders**

Insiders already have legitimate access. They know where sensitive data is stored, how to bypass security controls, and their activity may look normal. The Tesla insider case showed how one employee could cause massive damage.

</details>

---

**Congratulations on completing Session 2!** 🎉

You now understand the fundamentals of cloud security - the most critical aspect of cloud computing. Remember: most breaches aren't sophisticated hacks, they're simple misconfigurations that YOU can prevent!

---

## Quick Reference Card

```
┌─────────────────────────────────────────────────────────────────────┐
│           CLOUD SECURITY ESSENTIALS - QUICK REFERENCE               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  🔐 THE BIG 3 RULES:                                               │
│  1. NEVER use root account for daily work                           │
│  2. ALWAYS enable MFA on all users                                  │
│  3. NEVER allow SSH/RDP from 0.0.0.0/0                              │
│                                                                     │
│  📋 SHARED RESPONSIBILITY:                                          │
│  • AWS = Security OF the cloud (hardware, network, hypervisor)      │
│  • YOU = Security IN the cloud (data, IAM, config, patching)        │
│                                                                     │
│  🛡️ SECURITY SERVICES CHEAT SHEET:                                 │
│  ┌─────────────────┬──────────────────────────────────────────┐     │
│  │ Need            │ Use                                      │     │
│  ├─────────────────┼──────────────────────────────────────────┤     │
│  │ Firewall        │ Security Groups (instance) / NACL (subnet)│     │
│  │ User access     │ IAM (users, groups, roles, policies)     │     │
│  │ Encrypt data    │ KMS (keys) + S3/RDS/EBS encryption       │     │
│  │ Audit logs      │ CloudTrail (all API calls)               │     │
│  │ Threat detection│ GuardDuty (ML-powered)                   │     │
│  │ Find PII        │ Macie (scans S3 for sensitive data)      │     │
│  │ DDoS protection │ Shield (free) / Shield Advanced          │     │
│  │ Compliance      │ AWS Config (continuous rule checking)    │     │
│  └─────────────────┴──────────────────────────────────────────┘     │
│                                                                     │
│  💰 FREE TIER REMINDER:                                             │
│  • EC2 t2.micro: 750 hrs/month (12 months)                          │
│  • S3: 5GB storage                                                  │
│  • Lambda: 1M requests/month                                        │
│  • CloudTrail: 90 days free event history                           │
│  • GuardDuty: 30-day free trial                                     │
│                                                                     │
│  ⚠️ SET $10 BILLING ALERT TODAY!                                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```
