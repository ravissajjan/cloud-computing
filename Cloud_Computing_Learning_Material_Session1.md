# Cloud Management Mechanisms

## Table of Contents

| Section | Topic | Page |
|---------|-------|------|
| **Basics** | | |
| - | [Key Terms Glossary](#key-terms-glossary) | Quick reference |
| - | [AWS Terminology Cheat Sheet](#aws-terminology-cheat-sheet) | Before labs |
| **Theory** | | |
| 1.1 | [Cloud Computing Fundamentals](#11-cloud-computing-fundamentals) | Definition, service models |
| 1.2 | [Cloud Management Mechanisms Overview](#12-cloud-management-mechanisms-overview) | The 9 mechanisms |
| 1.3 | [Workload Distribution (Load Balancing)](#13-workload-distribution-load-balancing) | ALB, algorithms |
| 1.4 | [Elastic Resource Capacity (Auto-Scaling)](#14-elastic-resource-capacity-auto-scaling) | ASG, policies |
| 1.5 | [Multi-Cloud Architecture](#15-multi-cloud-architecture) | Vendor independence |
| 1.6 | [Hypervisor Clustering](#16-hypervisor-clustering) | VM high availability |
| 1.7 | [Cloud Balancing (DNS Routing)](#17-cloud-balancing-dns-based-routing) | Route 53 |
| 1.8 | [Edge Computing](#18-edge-computing) | Low latency |
| 1.9 | [Fog Computing](#19-fog-computing) | IoT middle layer |
| 1.10 | [Metacloud & Federated Cloud](#110-metacloud--federated-cloud) | Multi-cloud mgmt |
| **Hands-On Labs** | | |
| 1.11 | [Lab: Deploy Scalable Web App](#111-lab-exercise-deploy-scalable-web-app) | ALB + Auto Scaling (35 min) |
| 1.12 | [Lab: S3 Static Website](#112-lab-exercise-s3-static-website-hosting) | Serverless hosting (20 min) |
| **Wrap-Up** | | |
| - | [Summary & Key Takeaways](#summary-key-takeaways) | Learning outcomes |
| - | [Preview: Session 2 Security](#preview-why-session-2-security-matters) | Capital One case study |

---

## Course Overview
| Aspect | Details |
|--------|---------|
| Duration | 6 hours (2 × 3 hours) |
| Target | B.Tech/M.tech CSE students |
| Prerequisites | Basic networking, OS concepts, Linux commands |
| Tools | AWS Free Tier, Terraform (optional - for reference only) |

---

## Key Terms Glossary

Refer back to this when you encounter unfamiliar terms:

| Term | Simple Meaning |
|------|----------------|
| Server | A powerful computer that serves data to other computers |
| Virtual Machine (VM) | A "fake" computer running inside a real computer |
| Instance | AWS word for "virtual machine" or "server" |
| Latency | Delay/waiting time (like ping in games) |
| Scalability | Ability to grow bigger when needed |
| Region | Geographic location of AWS data centers (Mumbai, Virginia, Singapore) |
| Availability Zone (AZ) | Separate data center WITHIN a region |
| High Availability (HA) | System stays up even if parts fail |
| Load | How busy/stressed a server is (like CPU usage) |
| Firewall | Security guard that blocks unwanted network traffic |
| Encryption | Scrambling data so only authorized people can read it |
| Mechanism | A method or technique to achieve something |
| API | Application Programming Interface - how programs talk to each other |
| Throughput | Amount of data processed per unit time |

---

# Session 1: Resource & Architecture Mechanisms (3 Hours)

## 1.1 Cloud Computing Fundamentals

### Definition
Cloud computing = On-demand delivery of compute, storage, and networking resources over the internet with pay-per-use pricing.

### Cloud vs On-Premises Comparison

| Aspect | On-Premises | Cloud |
|--------|-------------|-------|
| CapEx | High (buy hardware) | Low (rent resources) |
| Scaling Time | Weeks/months | Minutes |
| Maintenance | Your responsibility | Provider managed |
| Global Deployment | Build data centers | Already available |
| Disaster Recovery | Complex, expensive | Built-in options |

### Visual: Traditional vs Cloud
```
┌─────────────────────────────────────┬─────────────────────────────────────┐
│         TRADITIONAL (On-Prem)       │            CLOUD                    │
├─────────────────────────────────────┼─────────────────────────────────────┤
│                                     │                                     │
│   ┌─────────────────────┐           │        ☁️ ☁️ ☁️ ☁️                │
│   │   Your Server Room  │           │      ☁️  INTERNET  ☁️              │
│   │   ┌───┐ ┌───┐ ┌───┐ │           │        ☁️ ☁️ ☁️ ☁️                │
│   │   │|||│ │|||│ │|||│ │           │              │                      │
│   │   │|||│ │|||│ │|||│ │           │              ▼                      │
│   │   └───┘ └───┘ └───┘ │           │   ┌─────────────────────┐           │
│   │     Your Servers    │           │   │  AWS/Azure/Google   │           │
│   └─────────────────────┘           │   │  Data Center        │           │
│                                     │   │  (They manage it!)  │           │
│   YOU buy, maintain, cool,          │   └─────────────────────┘           │
│   power, secure, replace            │                                     │
│                                     │   YOU just use it & pay monthly     │
│   💰💰 $100K+ upfront              │   💰 Pay per hour/month             │
└─────────────────────────────────────┴─────────────────────────────────────┘
```

### Service Models
```
┌────────────────────────────────────────────────────────┐
│  SaaS    │ Gmail, Salesforce         │ Use application │
├──────────┼───────────────────────────┼─────────────────┤
│  PaaS    │ Heroku, AWS Elastic       │ Deploy code     │
│          │ Beanstalk                 │                 │
├──────────┼───────────────────────────┼─────────────────┤
│  IaaS    │ EC2, Azure VMs            │ Manage VMs      │
└──────────┴───────────────────────────┴─────────────────┘
```

### Major Providers (2026)
- **AWS (~31%)**: Most services, largest market share
- **Azure (~25%)**: Enterprise integration, Microsoft ecosystem
- **GCP (~12%)**: AI/ML, Kubernetes-native

### Why Should YOU Care About Cloud?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CAREER IMPACT                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  📈 SALARY BOOST:                                                   │
│     • Cloud skills = 20-30% higher salary                           │
│     • AWS certification = avg $25K more per year                    │
│                                                                     │
│  💼 JOB DEMAND:                                                     │
│     • 2026: 4.5 million cloud jobs unfilled globally                │
│     • Every company is moving to cloud                              │
│                                                                     │
│  🎯 ROLES THAT NEED THIS:                                           │
│     • Software Developer      • DevOps Engineer                     │
│     • System Administrator    • Solutions Architect                 │
│     • Data Engineer           • Security Engineer                   │
│                                                                     │
│  "If you're in IT and don't know cloud, you're falling behind."     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 1.2 Cloud Management Mechanisms Overview

### What Are Cloud Management Mechanisms?

```
╔══════════════════════════════════════════════════════════════════════╗
║  DEFINITION                                                          ║
╠══════════════════════════════════════════════════════════════════════╣
║                                                                      ║
║  Cloud Management Mechanisms are the tools, technologies, and        ║
║  architectural patterns used to GOVERN, OPERATE, and OPTIMIZE        ║
║  cloud computing resources.                                          ║
║                                                                      ║
║  Think of them as the "operating system" for managing the cloud:     ║
║                                                                      ║
║  • HOW do we distribute work across servers?      → Load Balancing   ║
║  • HOW do we grow/shrink resources automatically? → Auto-Scaling     ║
║  • HOW do we manage multiple cloud providers?     → Multi-Cloud      ║
║  • HOW do we ensure high availability?            → Clustering       ║
║  • HOW do we secure cloud resources?              → Security Groups  ║
║  • HOW do we monitor and control costs?           → Cloud Governance ║
║                                                                      ║
╚══════════════════════════════════════════════════════════════════════╝
```

**In Short:** Cloud Management Mechanisms = techniques to manage rented cloud resources efficiently, securely, and cost-effectively.

### The 4 Pillars of Cloud Management

```
    ┌─────────────────────────────────────────────────────────────────────┐
    │                                                                     │
    │   1. RESOURCE MANAGEMENT                                            │
    │      └── Load Balancing, Auto-Scaling, Clustering                   │
    │          "How to use cloud resources efficiently"                   │
    │                                                                     │
    │   2. ARCHITECTURE PATTERNS                                          │
    │      └── Edge, Fog, Metacloud, Federated, Multi-Cloud               │
    │          "How to structure your cloud deployment"                   │
    │                                                                     │
    │   3. SECURITY MECHANISMS                                            │
    │      └── IAM, Security Groups, Encryption, DLP                      │
    │          "How to protect your cloud resources"                      │
    │                                                                     │
    │   4. GOVERNANCE & MONITORING                                        │
    │      └── CloudTrail, GuardDuty, Cost Management                     │
    │          "How to control and audit your cloud"                      │
    │                                                                     │
    └─────────────────────────────────────────────────────────────────────┘
```

### The 9 Core Mechanisms

The **9 core mechanisms** for managing cloud resources:

| Category | Mechanisms | Purpose |
|----------|------------|---------|
| **Resource Management** | Workload Distribution, Elastic Capacity, Hypervisor Clustering | Efficient resource utilization |
| **Architecture Patterns** | Multi-Cloud, Cloud Balancing, Metacloud, Federated | Deployment strategies |
| **Edge Processing** | Edge Computing, Fog Computing | Low-latency processing |

---

## 1.3 Workload Distribution (Load Balancing)

### Concept
Distributes incoming traffic across multiple backend servers to prevent overload and ensure high availability.

### Real-World Analogy: Restaurant Hostess
```
Imagine a busy restaurant with 5 chefs:
- Without hostess: All customers crowd one chef → long wait, chef burns out
- With hostess: Distributes orders evenly → faster service, happy customers

Load Balancer = Digital Hostess for your servers
```

### Student Analogy: College Fest Website
```
🖥️ COLLEGE FEST WEBSITE SCENARIO:

Your college fest website goes live. 10,000 students try to register...

WITHOUT Load Balancer:
─────────────────────────────────────────────────────────────────
All 10,000 requests → 1 Server (your ₹500/month VPS)
Server: "HELP! CPU 100%! RAM full! I'm crashing!"
Result: Website shows "502 Bad Gateway" = Registration FAILED

WITH Load Balancer:
─────────────────────────────────────────────────────────────────
Load Balancer receives 10,000 requests
→ 2,500 requests to Server 1
→ 2,500 requests to Server 2
→ 2,500 requests to Server 3  
→ 2,500 requests to Server 4
Result: Each server handles 2,500 = Registration works smoothly!

It's like nginx reverse proxy you might have used, but smarter!
```

### Application Examples
| App | How Load Balancing Helps |
|-----|-------------------------|
| **Flipkart Big Billion Days** | 10M users hit site simultaneously → distributed across 1000s of servers |
| **IRCTC Tatkal** | 10 AM rush → requests spread to prevent crash |
| **WhatsApp** | 2B users → messages routed to nearest healthy server |
| **YouTube** | Video requests distributed based on viewer location |

### Architecture
```
                    Users (10,000 req/sec)
                           │
                           ▼
                   ┌───────────────┐
                   │ Load Balancer │
                   └───────┬───────┘
           ┌───────────────┼───────────────┐
           ▼               ▼               ▼
      ┌─────────┐    ┌─────────┐    ┌─────────┐
      │Server 1 │    │Server 2 │    │Server 3 │
      │ 3.3K/s  │    │ 3.3K/s  │    │ 3.3K/s  │
      └─────────┘    └─────────┘    └─────────┘
```

### Load Balancing Algorithms

| Algorithm | Description | Use Case |
|-----------|-------------|----------|
| Round Robin | Sequential distribution | Equal server capacity |
| Weighted Round Robin | Based on server capacity | Heterogeneous servers |
| Least Connections | Routes to least busy server | Long-lived connections |
| IP Hash | Same client → same server | Session persistence |
| Least Response Time | Routes to fastest server | Performance-critical apps |

### AWS Implementation

**Application Load Balancer (ALB)** - Layer 7, HTTP/HTTPS routing

```bash
# Create ALB using AWS CLI
aws elbv2 create-load-balancer \
    --name my-alb \
    --subnets subnet-12345 subnet-67890 \
    --security-groups sg-12345

# Create Target Group
aws elbv2 create-target-group \
    --name my-targets \
    --protocol HTTP \
    --port 80 \
    --vpc-id vpc-12345 \
    --health-check-path /health

# Register EC2 instances
aws elbv2 register-targets \
    --target-group-arn arn:aws:elasticloadbalancing:... \
    --targets Id=i-instance1 Id=i-instance2
```

**Health Checks Configuration:**
- Path: `/health` or `/`
- Interval: 30 seconds
- Healthy threshold: 2 consecutive successes
- Unhealthy threshold: 2 consecutive failures

### Real-World Example: Netflix
- 280M users globally
- Uses AWS ELB with thousands of backend instances
- Geographic routing via Route 53 + regional ALBs

> **WOW Moment: The Scale of Cloud**
> - AWS has 1.5 MILLION+ customers
> - Netflix serves 280 million users on AWS
> - AWS adds server capacity DAILY equivalent to Amazon.com at $7 BILLION revenue

---

### Self-Check Question #1

**Q: If you have 3 servers behind a load balancer and one crashes, what happens?**

<details>
<summary>Click to see answer</summary>

**A:** The load balancer automatically detects the failed server via health checks and redirects all traffic to the 2 remaining healthy servers. Users may not even notice!

</details>

---

## 1.4 Elastic Resource Capacity (Auto-Scaling)

### Concept
Automatically adjusts compute capacity based on demand. Scales OUT (add instances) or IN (remove instances).

### Real-World Analogy: Uber Surge Pricing
```
Uber during normal hours: 100 drivers active
Uber on New Year's Eve: 500 drivers active (auto-scaled!)
Uber at 3 AM: 20 drivers active (scaled down)

You don't pay 500 drivers 24/7 — only when needed.
Same with cloud servers!
```

### Student Analogy: Coding Lab Computers
```
💻 COLLEGE COMPUTER LAB SCENARIO:

Think of your college computer lab during different times:

• 6 AM (empty lab)      → 2 PCs running (just for security cams)
• 10 AM (lab session)   → 50 PCs running (full class)
• 3 AM (deadline night) → 100 PCs running (everyone submitting!)
• After submissions     → Back to 5 PCs (cleanup)
```

### Auto-Scaling Timeline Graph
```
                        ELASTIC RESOURCE CAPACITY (AUTO-SCALING)
    
    Number of
    Servers
        │
     10 │                              ████████
      9 │                           ███        ███
      8 │                         ██              ██
      7 │                        █                  █
      6 │                       █                    █
      5 │                      █                      █
      4 │                     █                        █
      3 │         ████████████                          ████████████
      2 │    █████                                                 █████
      1 │████                                                           ████
        └────┬────────┬────────┬────────┬────────┬────────┬────────┬────────►
           12am     6am      12pm     6pm     12am     6am      12pm    Time
           
                        Normal │ Traffic ││ Peak │ Traffic │ Normal
                               │  Rises  ││      ││ Drops  │
    
    RESULT: Pay for 10 servers only during peak, not 24/7!
```

### Vertical vs Horizontal Scaling
```
┌─────────────────────────────────────────────────────────────────────┐
│                    TWO WAYS TO SCALE                                │
├──────────────────────────────┬──────────────────────────────────────┤
│   VERTICAL (Scale UP)        │   HORIZONTAL (Scale OUT)             │
│   "Bigger Server"            │   "More Servers"                     │
├──────────────────────────────┼──────────────────────────────────────┤
│                              │                                      │
│   Before:    After:          │   Before:         After:             │
│   ┌─────┐   ┌───────┐        │   ┌────┐         ┌────┐ ┌────┐       │
│   │ 4GB │   │ 32GB  │        │   │ S1 │         │ S1 │ │ S2 │       │
│   │ RAM │   │  RAM  │        │   └────┘         └────┘ └────┘       │
│   │     │   │       │        │                  ┌────┐ ┌────┐       │
│   └─────┘   │       │        │                  │ S3 │ │ S4 │       │
│             └───────┘        │                  └────┘ └────┘       │
│                              │                                      │
│   ✓ Simple                   │   ✓ No single point of failure      │
│   ✗ Has limits               │   ✓ Unlimited scaling               │
│   ✗ Downtime to upgrade      │   ✗ More complex                    │
└──────────────────────────────┴──────────────────────────────────────┘

CLOUD BEST PRACTICE: Horizontal scaling (add more servers)
```

### Application Examples
| App | Scaling Scenario |
|-----|------------------|
| **Hotstar IPL Final** | 2 servers → 2000 servers in 10 minutes |
| **Zomato lunch hour** | 12-2 PM: 5x servers, scales down by 4 PM |
| **E-commerce flash sale** | Pre-scheduled scale-up before sale starts |
| **SaaS product** | Weekdays busy, weekends quiet → auto-adjusts |

### Scaling Types

| Type | Description | Example |
|------|-------------|---------|
| **Horizontal** | Add/remove instances | 2 servers → 10 servers |
| **Vertical** | Resize instance | t2.micro → t2.large |

**Best Practice:** Prefer horizontal scaling (no single point of failure, unlimited capacity).

### Scaling Policies

```
┌─────────────────────────────────────────────────────┐
│ Target Tracking: Maintain CPU at 70%                │
│ Step Scaling: +2 instances when CPU > 80%           │
│ Scheduled: Scale to 10 instances at 9 AM daily      │
└─────────────────────────────────────────────────────┘
```

### AWS Auto Scaling Configuration

```bash
# Create Launch Template
aws ec2 create-launch-template \
    --launch-template-name my-template \
    --launch-template-data '{
        "ImageId": "ami-12345",
        "InstanceType": "t3.micro",
        "SecurityGroupIds": ["sg-12345"]
    }'

# Create Auto Scaling Group
aws autoscaling create-auto-scaling-group \
    --auto-scaling-group-name my-asg \
    --launch-template LaunchTemplateName=my-template \
    --min-size 2 \
    --max-size 10 \
    --desired-capacity 2 \
    --vpc-zone-identifier "subnet-1,subnet-2"

# Create Target Tracking Policy
aws autoscaling put-scaling-policy \
    --auto-scaling-group-name my-asg \
    --policy-name cpu-target-tracking \
    --policy-type TargetTrackingScaling \
    --target-tracking-configuration '{
        "PredefinedMetricSpecification": {
            "PredefinedMetricType": "ASGAverageCPUUtilization"
        },
        "TargetValue": 70.0
    }'
```

### Terraform Example (Optional - For Reference)

> **Note:** This section is for students interested in Infrastructure as Code. Skip if you're focusing on AWS Console.

```hcl
resource "aws_autoscaling_group" "web" {
  name                = "web-asg"
  vpc_zone_identifier = var.subnet_ids
  min_size            = 2
  max_size            = 10
  desired_capacity    = 2

  launch_template {
    id      = aws_launch_template.web.id
    version = "$Latest"
  }

  target_group_arns = [aws_lb_target_group.web.arn]

  tag {
    key                 = "Name"
    value               = "web-server"
    propagate_at_launch = true
  }
}

resource "aws_autoscaling_policy" "cpu" {
  name                   = "cpu-policy"
  autoscaling_group_name = aws_autoscaling_group.web.name
  policy_type            = "TargetTrackingScaling"

  target_tracking_configuration {
    predefined_metric_specification {
      predefined_metric_type = "ASGAverageCPUUtilization"
    }
    target_value = 70.0
  }
}
```

### Cost Impact
- **Without auto-scaling:** Pay for peak capacity 24/7
- **With auto-scaling:** Pay for actual usage
- **Savings:** 40-70% for variable workloads

> **WOW Moment: Hotstar IPL Scaling**
> - Normal days: ~200 servers
> - IPL Final 2024: Scaled to 2,000+ servers in 10 minutes
> - After match: Back to 200 servers
> - **Savings:** ₹10+ crore/month by not running 2000 servers 24/7

---

### Self-Check Question #2

**Q: Uber gets 10x more ride requests on New Year's Eve. Should they scale vertically or horizontally?**

<details>
<summary>Click to see answer</summary>

**A:** Horizontally (add more servers). Reasons:
- Vertical scaling has limits (can't make one server infinitely powerful)
- Horizontal provides redundancy (no single point of failure)
- Can scale to ANY level needed
- Faster to add 100 small servers than upgrade one massive server

</details>

---

## 1.5 Multi-Cloud Architecture

### Concept
Using multiple cloud providers simultaneously to avoid vendor lock-in, leverage provider strengths, and improve resilience.

### Real-World Analogy: Investment Portfolio
```
Don't put all your money in one stock!
- Stocks (AWS) + Bonds (Azure) + Gold (GCP) = Diversified portfolio
- If one crashes, others keep you stable

Same logic: Don't put all workloads on one cloud provider.
```

### Student Analogy: Git Backup Strategy
```
🔄 GIT REMOTE BACKUP ANALOGY:

You have your final year project code...

SINGLE CLOUD = Code only on GitHub
───────────────────────────────
    📁 GitHub only
    └── fyp-project/
    
    RISK: GitHub goes down (happened in 2020!) = Can't access code!
          Or account gets hacked = Everything deleted!

MULTI-CLOUD = Code on multiple remotes
───────────────────────────────
    📁 GitHub (primary)    📁 GitLab (backup)    📁 Bitbucket (backup)
    └── fyp-project/       └── fyp-project/      └── fyp-project/
    
    Command: git push --all  (pushes to all remotes)
    
    BENEFIT: GitHub down? Pull from GitLab! 
             GitLab hacked? Still have Bitbucket!

💡 Real companies do this: Netflix runs on AWS but can failover to 
   their own data centers. Spotify uses both GCP and AWS.
```

### Application Examples
| Company | Multi-Cloud Strategy |
|---------|----------------------|
| **Twitter/X** | AWS (compute) + GCP (data analytics) |
| **Spotify** | GCP (ML/data) + AWS (some services) |
| **Adobe** | AWS + Azure for redundancy |
| **Banks in India** | RBI mandates: No single cloud dependency |

### Architecture Pattern
```
┌─────────────────────────────────────────────────────┐
│                  Your Application                   │
├─────────────────┬─────────────────┬─────────────────┤
│       AWS       │      Azure      │       GCP       │
│   (Compute)     │  (Enterprise)   │    (AI/ML)      │
│                 │                 │                 │
│   EC2, S3       │   AD, Teams     │  BigQuery, AI   │
└─────────────────┴─────────────────┴─────────────────┘
```

### When to Use Multi-Cloud

| Scenario | Benefit |
|----------|---------|
| Regulatory compliance | Data residency requirements |
| Vendor negotiation | Better pricing leverage |
| Best-of-breed services | Use each provider's strengths |
| Disaster recovery | Cross-provider failover |

### Challenges
- Increased complexity (multiple CLIs, APIs, skills)
- Data transfer costs between clouds
- Inconsistent security policies
- Higher operational overhead

### Implementation Tools
- **Terraform**: Infrastructure as Code across clouds
- **Kubernetes**: Container orchestration portability
- **Pulumi**: Multi-cloud IaC with programming languages

---

### Self-Check Question #3

**Q: A company uses AWS for everything. Their CTO is worried. Why might multi-cloud be a good idea?**

<details>
<summary>Click to see answer</summary>

**A:** Several reasons:
1. **Vendor lock-in risk** - If AWS raises prices, you have no leverage
2. **Outages** - AWS had major outages in 2021, 2023 affecting thousands of companies
3. **Compliance** - Some regulations (RBI for banks) require multi-cloud
4. **Best-of-breed** - Google has better AI/ML, Azure better for Microsoft enterprise
5. **Negotiating power** - "We can move to Azure" gets you better AWS pricing

</details>

---

## 1.6 Hypervisor Clustering

### Concept
Multiple hypervisors work together to provide VM high availability. If one physical host fails, VMs automatically migrate to healthy hosts.

### Hypervisor Architecture
```
┌────────────────────────────────────────────────────┐
│                 HYPERVISOR CLUSTER                 │
├────────────┬────────────┬────────────┬─────────────┤
│  Host 1    │  Host 2    │  Host 3    │  Host 4     │
│ ┌──┐ ┌──┐  │ ┌──┐ ┌──┐  │ ┌──┐ ┌──┐  │ ┌──┐ ┌──┐   │
│ │VM│ │VM│  │ │VM│ │VM│  │ │VM│ │VM│  │ │VM│ │VM│   │
│ └──┘ └──┘  │ └──┘ └──┘  │ └──┘ └──┘  │ └──┘ └──┘   │
├────────────┴────────────┴────────────┴─────────────┤
│                  Shared Storage (SAN)              │
└────────────────────────────────────────────────────┘
```

### Hypervisor Types

| Type 1 (Bare Metal) | Type 2 (Hosted) |
|---------------------|-----------------|
| Runs directly on hardware | Runs on host OS |
| VMware ESXi, Xen, Hyper-V | VirtualBox, VMware Workstation |
| Production use | Development/testing |

### AWS Implementation
- **Nitro System**: AWS's custom hypervisor
- **Placement Groups**: Control VM placement for HA
- **Dedicated Hosts**: Full control over host placement

---

## 1.7 Cloud Balancing (DNS-Based Routing)

### Concept
Intelligent traffic routing based on user location, server health, or custom policies using DNS.

### Routing Policies

| Policy | Description | Use Case |
|--------|-------------|----------|
| Simple | Single resource | Basic setup |
| Weighted | Percentage-based split | A/B testing, gradual migration |
| Latency | Lowest latency region | Global applications |
| Geolocation | User's geographic location | Regional content |
| Failover | Active-passive HA | Disaster recovery |
| Multivalue | Multiple healthy records | Load distribution |

### AWS Route 53 Configuration

```bash
# Create hosted zone
aws route53 create-hosted-zone \
    --name example.com \
    --caller-reference $(date +%s)

# Latency-based routing
aws route53 change-resource-record-sets \
    --hosted-zone-id Z123456 \
    --change-batch '{
        "Changes": [{
            "Action": "CREATE",
            "ResourceRecordSet": {
                "Name": "api.example.com",
                "Type": "A",
                "SetIdentifier": "us-east",
                "Region": "us-east-1",
                "TTL": 60,
                "ResourceRecords": [{"Value": "10.0.0.1"}]
            }
        }]
    }'
```

### Simple Example: Swiggy/Zomato Order
```
You order biryani on Swiggy:
- You're in Koramangala → order goes to nearest Koramangala restaurant
- Your friend in Indiranagar → order goes to Indiranagar outlet
- NOT routed to a single restaurant in Whitefield for all of Bangalore!

Route 53 does the same — routes users to nearest server.
```

### Global Architecture
```
User (India) ──► Route 53 ──► Mumbai Region (20ms)
User (USA)   ──► Route 53 ──► Virginia Region (15ms)
User (EU)    ──► Route 53 ──► Frankfurt Region (25ms)
```

> **WOW Moment: Spotify's Global Magic**
> - 640 MILLION users, 120 million songs
> - Press play → music starts in <200 MILLISECONDS
> - Secret: 18 AWS regions routing you to the NEAREST server
> - Without geo-routing: 500ms+ latency, constant buffering

---

## 1.8 Edge Computing

### Concept
Process data at the network edge (near data source) instead of centralized cloud, reducing latency for time-sensitive applications.

### Student Analogy: Online Gaming (VALORANT/PUBG)
```
🎮 ONLINE GAMING LATENCY SCENARIO:

You're playing a competitive FPS game...

CLOUD-ONLY COMPUTING:
─────────────────────────
1. You press 'Fire' button
2. Signal goes to server in Singapore (50ms)
3. Server calculates hit detection (10ms)
4. Result comes back to you (50ms)
TOTAL: 110ms ping → Enemy already moved! You MISS!

EDGE COMPUTING:
─────────────────────────
1. You press 'Fire' button
2. Your GPU predicts hit locally (5ms)
3. Shows result immediately, syncs with server later
TOTAL: 5ms response → Perfect headshot! You WIN!

IN REAL CLOUD:
• Self-driving car: 200ms cloud delay = CRASH into pedestrian!
• Edge processing: 5ms local decision = BRAKES in time!
• Your game client does edge computing for smooth gameplay!
```

### Edge vs Cloud Processing - Detailed
```
                    TRADITIONAL CLOUD vs EDGE COMPUTING
    
    TRADITIONAL CLOUD:
    ══════════════════
    
    📱 Device          ☁️☁️☁️ CLOUD ☁️☁️☁️          📱 Device
    (Car Camera)                                    (Response)
         │                    │                         ▲
         │    1. Send data    │                         │
         │    ───────────────►│                         │
         │    (100ms)         │    3. Return result     │
         │                    │    ◄───────────────     │
         │                    │    (100ms)              │
         └────────────────────┴─────────────────────────┘
                          
         TOTAL: 200ms+ round trip
         ❌ TOO SLOW for self-driving car!
    
    
    EDGE COMPUTING:
    ═══════════════
    
    📱 Device     🖥️ Edge Computer     ☁️☁️☁️ CLOUD ☁️☁️☁️
    (Car Camera)  (In the Car!)         (For storage/learning)
         │              │                      │
         │  1. Send     │                      │
         │  ──────────► │                      │
         │  (<1ms)      │  2. Process locally  │
         │              │  "Is that a person?" │
         │  3. Result   │  "YES! BRAKE!"       │
         │  ◄────────── │                      │
         │  (<1ms)      │                      │
         │              │  4. Send to cloud    │
         │              │  later (for          │
         │              │  improving AI)       │
         │              │  ───────────────►    │
    
         TOTAL: <5ms decision
         ✅ Fast enough to save lives!
```

### Real-World Analogy: ATM vs Bank Branch
```
Withdraw ₹500:
- Bank Branch (Cloud): Drive 5km, wait in queue, 30 min → HIGH LATENCY
- ATM nearby (Edge): Walk 100m, instant cash → LOW LATENCY

Edge = Mini data centers close to users
```

### Application Examples
| Application | Why Edge is Critical |
|-------------|----------------------|
| **Self-driving cars** | Can't wait 200ms for "STOP" decision → edge AI in car |
| **PUBG/Valorant** | 10ms vs 100ms = win vs lose → edge game servers |
| **Factory robots** | Emergency stop must be instant → local processing |
| **Netflix** | Videos cached at edge (ISP level) → no buffering |

### When Latency Matters

| Application | Required Latency | Solution |
|-------------|------------------|----------|
| Autonomous vehicles | < 10ms | Edge processing |
| AR/VR gaming | < 20ms | Edge + 5G |
| Industrial IoT | < 50ms | Fog computing |
| Video streaming | < 100ms | CDN caching |

### Edge vs Cloud Processing
```
CLOUD PROCESSING:              EDGE PROCESSING:
Device → Cloud → Device        Device → Edge → Device
(100-200ms round trip)         (<10ms processing)
```

### AWS Edge Services

| Service | Purpose |
|---------|---------|
| **CloudFront** | CDN for static content |
| **Lambda@Edge** | Run code at edge locations |
| **Outposts** | AWS infrastructure on-premises |
| **Wavelength** | AWS at 5G carrier edge |
| **Snow Family** | Portable edge computing |

### Lambda@Edge Example

```javascript
// Modify response headers at edge
exports.handler = async (event) => {
    // Get the response from CloudFront
    const response = event.Records[0].cf.response;
    const headers = response.headers;
    
    // Add HSTS header - tells browsers "always use HTTPS"
    headers['strict-transport-security'] = [{
        key: 'Strict-Transport-Security',
        value: 'max-age=31536000; includeSubdomains'
    }];
    
    return response;
};
```

**Simple Explanation:**
```
Think of it like Jio/Airtel towers across India:
- Without Lambda@Edge: Every call goes to Mumbai HQ for processing
- With Lambda@Edge: Call handled by nearest tower (Bangalore, Chennai, Delhi)

This code runs at 400+ CloudFront locations worldwide!
```

**What this code does:**
1. Intercepts every HTTP response at the edge (CloudFront location)
2. Adds HSTS header → forces browsers to always use HTTPS
3. `max-age=31536000` → browser remembers "use HTTPS" for 1 year

**Why it matters:** Security header added in <1ms at nearest location, not 200ms round-trip to origin server.

> **WOW Moment: Self-Driving Cars Need Edge**
> - Tesla generates 1 TERABYTE of data per hour
> - Must decide to brake in 5 MILLISECONDS
> - Cloud round-trip = 200ms = CRASH
> - Edge processing in the car = 5ms = SAFE

---

### Self-Check Question #4

**Q: An online game (PUBG/Valorant) needs 10ms response time. Where should the game logic run - Cloud, Fog, or Edge?**

<details>
<summary>Click to see answer</summary>

**A:** Edge (game client on player's device) + Fog (regional game servers):
- **Edge:** Player's device predicts movement, shows results immediately
- **Fog:** Regional servers (Mumbai, Singapore) handle authoritative game state
- **Cloud:** Only for matchmaking, leaderboards, replays (not time-sensitive)

This is why competitive games have "servers" in different regions - they're fog layer!

</details>

---

## 1.9 Fog Computing

### Concept
Intermediate layer between edge devices and cloud. Provides more processing power than edge devices but lower latency than cloud.

### Real-World Analogy: Regional Manager
```
Company structure:
- CEO (Cloud) → Makes big decisions, slow response
- Regional Manager (Fog) → Handles regional issues quickly
- Store Staff (Edge) → Handles immediate customer needs

Not everything needs CEO approval!
```

### Application Examples
| Industry | Fog Computing Use |
|----------|-------------------|
| **Smart City Traffic** | Local fog nodes adjust signals, cloud does city-wide planning |
| **Hospital ICU** | Fog aggregates patient vitals, alerts locally, sends trends to cloud |
| **Oil Rig Sensors** | Fog processes 10,000 sensor readings, sends only anomalies to cloud |
| **Smart Warehouse** | Fog coordinates robots locally, cloud manages inventory |

### Detailed Example: Smart City Traffic Management
```
    SMART CITY TRAFFIC MANAGEMENT
    ═════════════════════════════
    
    ┌────────────────────────────────────────────────────────────────────┐
    │                                                                    │
    │  EDGE: Traffic cameras at intersections                            │
    │  ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐                      │
    │  │ 📷  │ │ 📷   │ │ 📷   │ │ 📷  │ │ 📷   │                     │
    │  │Detect│ │Detect│ │Detect│ │Detect│ │Detect│  ← 10,000 sensors    │
    │  │cars  │ │cars  │ │cars  │ │cars  │ │cars  │    Simple counting   │
    │  └──┬───┘ └──┬───┘ └──┬───┘ └──┬───┘ └──┬───┘                      │
    │     │        │        │        │        │                          │
    │     └────────┴────────┼────────┴────────┘                          │
    │                       │                                            │
    │                       ▼                                            │
    │  FOG: City-level servers (50 nodes across city)                    │
    │  ┌─────────────────────────────────────────────┐                   │
    │  │ Analyze traffic patterns across 200 cameras │  ← Moderate       │
    │  │ "Heavy traffic on Main St, adjust signals"  │    processing     │
    │  │ Make REAL-TIME decisions for the area       │    Regional view  │
    │  └─────────────────────────────────────────────┘                   │
    │                       │                                            │
    │                       │ (Send summaries, not raw video)            │
    │                       ▼                                            │
    │  CLOUD: Central city planning (AWS)                                │
    │  ┌─────────────────────────────────────────────┐                   │
    │  │ Store historical data (years of patterns)   │  ← Massive        │
    │  │ Train AI models for better predictions      │    processing     │
    │  │ City-wide planning and optimization         │    Long-term view │
    │  └─────────────────────────────────────────────┘                   │
    │                                                                    │
    └────────────────────────────────────────────────────────────────────┘
```

### Computing Hierarchy
```
┌─────────────────────────────────────────────────────┐
│  CLOUD (AWS Region)    │ Unlimited compute, 50-200ms│
├────────────────────────┼────────────────────────────┤
│  FOG (Local Gateway)   │ Moderate compute, 1-10ms   │
├────────────────────────┼────────────────────────────┤
│  EDGE (Device)         │ Limited compute, <1ms      │
└────────────────────────┴────────────────────────────┘
```

### Use Case: Smart Factory
```
10,000 Sensors ──► Fog Gateway ──► AWS Cloud
    │                  │              │
    │ Raw data         │ Aggregated   │ Historical
    │ (1TB/hr)         │ data (1GB/hr)│ analysis
    │                  │              │
    └─ Local alerts    └─ Regional    └─ ML training
       (<1ms)             decisions       reports
```

### AWS IoT Greengrass
- Run Lambda functions on local devices
- Local messaging between devices
- Sync with cloud when connected
- Offline operation capability

---

## 1.10 Metacloud & Federated Cloud

### Metacloud
Single management plane for multiple clouds.

**Tools:**
- **Kubernetes**: Container orchestration across clouds
- **Terraform**: Unified IaC for all providers
- **Crossplane**: Kubernetes-native cloud management

### Federated Cloud
Independent organizations share resources while maintaining control.

**Characteristics:**
- Each organization keeps data sovereignty
- Shared compute resources via agreements
- Common identity federation (SSO)
- Example: European Grid Infrastructure (EGI)

---

## 📝 Architecture Quiz: Can You Identify the Mechanisms?

**Before moving to labs, test your understanding. Look at this architecture and identify which mechanisms are used:**

```
    QUESTION: Which architectures do you see here?
    
           👤 👤 👤 👤 👤 (Users from different countries)
                  │
                  ▼
         ┌────────────────┐
         │ Route 53 (DNS) │  ← What mechanism? 
         └───────┬────────┘
                 │
       ┌─────────┴─────────┐
       │                   │
       ▼                   ▼
    MUMBAI              VIRGINIA
    Region              Region
       │                   │
       ▼                   ▼
    ┌──────┐            ┌──────┐
    │ ALB  │            │ ALB  │  ← What mechanism?
    └──┬───┘            └──┬───┘
       │                   │
    ┌──┴──┐             ┌──┴──┐
    ▼     ▼             ▼     ▼
   EC2   EC2           EC2   EC2  ← What mechanism?
   (2-10) (Auto)       (2-10)(Auto)
```

<details>
<summary>Click to see answers</summary>

| Component | Mechanism | Explanation |
|-----------|-----------|-------------|
| **Route 53** | Cloud Balancing (DNS Routing) | Routes users to nearest region based on location/latency |
| **ALB** | Workload Distribution (Load Balancing) | Distributes traffic across EC2 instances |
| **EC2 (2-10 Auto)** | Elastic Resource Capacity (Auto-Scaling) | Scales instances between 2-10 based on demand |

**Bonus:** This architecture also demonstrates **Multi-Region** deployment for high availability!

</details>

---

## AWS Terminology Cheat Sheet

Read this before starting the labs:

| AWS Term | What It Actually Means |
|----------|------------------------|
| **EC2** | Elastic Compute Cloud = Virtual servers you rent by the hour |
| **Instance** | One virtual server (one EC2 = one instance) |
| **AMI** | Amazon Machine Image = Template/snapshot for a server (like Windows ISO) |
| **t2.micro** | Instance type: 1 vCPU, 1GB RAM (FREE TIER!) |
| **Region** | Geographic location (ap-south-1 = Mumbai) |
| **Availability Zone** | Separate data center within a region (ap-south-1a, ap-south-1b) |
| **VPC** | Virtual Private Cloud = Your private network bubble in AWS |
| **Subnet** | A slice of your VPC (like floors in a building) |
| **Security Group** | Firewall rules (who can connect to your server on which ports) |
| **CIDR** | IP range notation (10.0.0.0/16 = 65,536 IPs). Don't panic about it! |
| **Key Pair** | SSH key to log into your server (like a password file) |
| **Elastic IP** | A fixed public IP address (doesn't change on restart) |
| **IAM** | Identity and Access Management = Users, permissions, roles |
| **S3** | Simple Storage Service = Unlimited file storage (like Google Drive for servers) |
| **ARN** | Amazon Resource Name = Unique ID for any AWS resource |

**Free Tier Reminder:**
- t2.micro = FREE for 750 hours/month (that's 31 days 24/7!) for 12 months
- S3 = 5GB free storage
- Always check the "Free tier eligible" tag!

---

## 1.11 Lab Exercise: Deploy Scalable Web App

### Objective
Deploy a load-balanced, auto-scaling web application on AWS.

| Info | Details |
|------|---------|
| **Duration** | 35-45 minutes |
| **Cost** | Free Tier eligible (t2.micro) |
| **Region** | Mumbai (ap-south-1) recommended for India |
| **Prerequisites** | AWS account with Free Tier, basic AWS Console navigation |

### Architecture
```
Internet → ALB → Auto Scaling Group (2-4 instances)
                         │
                    ┌────┴────┐
                    │ Web     │
                    │ Servers │
                    │ (Apache)│
                    └─────────┘
```

### Step 1: Create Security Group

1. **Navigate:** EC2 Console → Security Groups → Create Security Group
2. **Configure:**
   - Name: `web-server-sg`
   - Description: "Allow HTTP from internet"
   - VPC: Default VPC
3. **Add Inbound Rules:**

| Type | Port | Source | Purpose |
|------|------|--------|---------|
| HTTP | 80 | 0.0.0.0/0 | Web traffic |
| SSH | 22 | My IP | Admin access (optional) |

4. Click **Create Security Group**

**Checkpoint:** Note down the Security Group ID (sg-xxxxx)

---

### Step 2: Create Launch Template

1. **Navigate:** EC2 → Launch Templates → Create Launch Template
2. **Configure:**
   - Name: `web-server-template`
   - AMI: Amazon Linux 2023 (Free Tier eligible)
   - Instance type: `t2.micro` (Free Tier)
   - Key pair: Select existing or create new
   - Security Group: Select `web-server-sg`

3. **Advanced Details → User Data:** (paste this script)
```bash
#!/bin/bash

# 1. Update the system (always do this first)
yum update -y

# 2. Install Apache web server + stress testing tool
yum install -y httpd stress

# 3. Start the web server
systemctl start httpd

# 4. Enable it to start on boot
systemctl enable httpd

# 5. Create webpage showing server hostname (so you can see load balancing work!)
cat <<EOF > /var/www/html/index.html
<!DOCTYPE html>
<html>
<head><title>Cloud Lab</title></head>
<body style="font-family: Arial; text-align: center; padding: 50px;">
  <h1>Hello from AWS!</h1>
  <h2>Server: $(hostname)</h2>
  <p>Instance ID: $(curl -s http://169.254.169.254/latest/meta-data/instance-id)</p>
  <p>Availability Zone: $(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone)</p>
</body>
</html>
EOF
```

4. Click **Create Launch Template**

**Checkpoint:** Launch Template created successfully

---

### Step 3: Create Target Group

1. **Navigate:** EC2 → Target Groups → Create Target Group
2. **Configure:**
   - Target type: Instances
   - Name: `web-targets`
   - Protocol: HTTP, Port: 80
   - VPC: Default VPC
   - Health check path: `/`

3. Click **Next** → **Create Target Group** (don't register targets manually)

**Checkpoint:** Target Group ARN noted

---

### Step 4: Create Application Load Balancer

1. **Navigate:** EC2 → Load Balancers → Create Load Balancer
2. **Select:** Application Load Balancer
3. **Configure:**
   - Name: `web-alb`
   - Scheme: Internet-facing
   - IP type: IPv4
   - VPC: Default VPC
   - Mappings: Select at least 2 Availability Zones (ap-south-1a, ap-south-1b)

4. **Security Group:** Create new or select existing allowing HTTP:80
5. **Listeners:**
   - HTTP:80 → Forward to `web-targets`

6. Click **Create Load Balancer**

**Checkpoint:** Note ALB DNS name (web-alb-xxxxx.ap-south-1.elb.amazonaws.com)

---

### Step 5: Create Auto Scaling Group

1. **Navigate:** EC2 → Auto Scaling Groups → Create Auto Scaling Group
2. **Step 1 - Choose template:**
   - Name: `web-asg`
   - Launch Template: `web-server-template`

3. **Step 2 - Choose instance launch options:**
   - VPC: Default VPC
   - Subnets: Select 2+ subnets (different AZs)

4. **Step 3 - Configure advanced options:**
   - Load balancing: Attach to existing load balancer (check the box)
   - Choose: `web-targets` target group
   - Health checks: Enable ELB health checks

5. **Step 4 - Configure group size:**
   - Desired: 2
   - Minimum: 2
   - Maximum: 4 (keep low for Free Tier)

6. **Step 5 - Configure scaling policies:**
   - Select: Target tracking scaling policy
   - Metric: Average CPU Utilization
   - Target value: 50 (lower for demo)

7. **Review and Create**

**Checkpoint:** ASG shows 2 instances launching

---

### Step 6: Test Your Setup

**6.1 Verify Load Balancing:**
1. Wait 2-3 minutes for instances to pass health checks
2. Open ALB DNS in browser: `http://web-alb-xxxxx.ap-south-1.elb.amazonaws.com`
3. Refresh multiple times — observe different hostnames (load balancing working!)

**6.2 Test Auto Scaling (Optional - generates CPU load):**

SSH into one instance and run:
```bash
# Generate CPU stress for 2 minutes
stress --cpu 4 --timeout 120
```

Or use CloudWatch to observe:
1. Navigate: CloudWatch → Alarms
2. Watch for scaling alarm trigger
3. Check EC2 console for new instances launching

**Success Criteria:**
- [ ] ALB DNS loads webpage
- [ ] Different server names on refresh
- [ ] Instances show "Healthy" in Target Group

---

### Step 7: Cleanup (IMPORTANT - Avoid charges!)

**Delete in this order:**

| Step | Resource | Navigation |
|------|----------|------------|
| 1 | Auto Scaling Group | EC2 → Auto Scaling Groups → Delete |
| 2 | Load Balancer | EC2 → Load Balancers → Delete |
| 3 | Target Group | EC2 → Target Groups → Delete |
| 4 | Launch Template | EC2 → Launch Templates → Delete |
| 5 | Security Group | EC2 → Security Groups → Delete `web-server-sg` |

**CLI Cleanup (alternative):**
```bash
# Delete ASG (terminates instances automatically)
aws autoscaling delete-auto-scaling-group \
    --auto-scaling-group-name web-asg \
    --force-delete

# Wait for instances to terminate, then delete ALB
aws elbv2 delete-load-balancer \
    --load-balancer-arn arn:aws:elasticloadbalancing:ap-south-1:...

# Delete Target Group
aws elbv2 delete-target-group \
    --target-group-arn arn:aws:elasticloadbalancing:ap-south-1:...
```

---

### Troubleshooting

| Problem | Solution |
|---------|----------|
| ALB shows "503 Service Unavailable" | Wait 2-3 min for health checks; verify Security Group allows HTTP |
| Instances stuck in "Unhealthy" | Check User Data script; SSH and verify Apache running (`systemctl status httpd`) |
| Can't access ALB DNS | Check ALB Security Group allows inbound HTTP:80 from 0.0.0.0/0 |
| Auto Scaling not triggering | Lower target CPU to 30%; generate more load |

### What You Learned
- Created Launch Template with User Data (bootstrap script)
- Configured Application Load Balancer for traffic distribution
- Set up Auto Scaling Group with target tracking policy
- Observed load balancing across multiple instances
- Understood the importance of proper cleanup

---

## 1.12 Lab Exercise: S3 Static Website Hosting

### Objective
Deploy a static website on S3 without managing any servers — auto-scales to millions of visitors.

| Info | Details |
|------|---------|
| **Duration** | 15-20 minutes |
| **Cost** | Free Tier: 5GB storage, 20K GET requests |
| **Region** | Mumbai (ap-south-1) recommended |
| **Prerequisites** | AWS account, basic HTML knowledge |

### Real-World Analogy: Google Drive vs Renting a Server Room
```
Hosting your portfolio website:

Traditional Way (EC2):
- Rent a server room (EC2 instance)
- Pay electricity 24/7 even with 0 visitors
- Manage security, updates, scaling yourself
- Cost: ₹500-2000/month minimum

S3 Way:
- Like putting files on Google Drive and sharing the link
- Pay only for storage + actual visitors
- AWS handles security, scaling, uptime
- Cost: ₹2-5/month for small sites!

S3 Static Hosting = Google Drive that auto-scales to Netflix traffic levels
```

### Application Examples
| Website Type | Why S3 is Perfect |
|--------------|-------------------|
| **Zerodha Varsity** | Documentation site — millions of readers, zero server management |
| **Razorpay Docs** | API documentation — scales during developer conferences |
| **Portfolio sites** | Students hosting resumes — costs ₹0-5/month |
| **React/Vue SPAs** | Single Page Apps — just HTML/CSS/JS, no backend needed |
| **Marketing landing pages** | Product launches — handles viral traffic spikes |

### Architecture
```
┌─────────────────────────────────────────────────────────┐
│                      INTERNET                           │
│                          │                              │
│                          ▼                              │
│  ┌───────────────────────────────────────────────────┐  │
│  │              S3 BUCKET (Website Enabled)          │  │
│  │  ┌─────────┐  ┌─────────┐  ┌─────────┐            │  │
│  │  │index.   │  │style.   │  │app.js   │            │  │
│  │  │html     │  │css      │  │         │            │  │
│  │  └─────────┘  └─────────┘  └─────────┘            │  │
│  │                                                   │  │
│  │  URL: bucket-name.s3-website-region.amazonaws.com │  │
│  └───────────────────────────────────────────────────┘  │
│                          │                              │
│              ┌───────────┴───────────┐                  │
│              ▼                       ▼                  │
│         1 visitor              1 million visitors       │
│         (same cost)            (auto-scales!)           │
└─────────────────────────────────────────────────────────┘
```

---

### Step 1: Create S3 Bucket

1. **Navigate:** AWS Console → S3 → Create Bucket
2. **Configure:**
   - Bucket name: `your-name-portfolio-2024` (must be globally unique!)
   - Region: Asia Pacific (Mumbai) ap-south-1
   - **Uncheck** "Block all public access" (required for website)
   - Acknowledge the warning checkbox

3. Click **Create Bucket**

**Why globally unique?** S3 bucket names are like domain names — only one `amazon-website` can exist worldwide.

**Checkpoint:** Bucket appears in your S3 bucket list

---

### Step 2: Enable Static Website Hosting

1. **Navigate:** Click your bucket → Properties tab
2. **Scroll to:** Static website hosting → Edit
3. **Configure:**
   - Static website hosting: **Enable**
   - Hosting type: Host a static website
   - Index document: `index.html`
   - Error document: `error.html` (optional)

4. Click **Save changes**

**Checkpoint:** Note the **Bucket website endpoint** URL (you'll need this!)
```
http://your-name-portfolio-2024.s3-website.ap-south-1.amazonaws.com
```

---

### Step 3: Create and Upload Website Files

**3.1 Create index.html locally:**
```html
<!DOCTYPE html>
<html>
<head>
    <title>My Cloud Portfolio</title>
    <style>
        body { 
            font-family: Arial; 
            text-align: center; 
            padding: 50px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            min-height: 100vh;
            margin: 0;
        }
        .container { 
            background: rgba(255,255,255,0.1); 
            padding: 40px; 
            border-radius: 20px;
            max-width: 600px;
            margin: 0 auto;
        }
        h1 { font-size: 2.5em; margin-bottom: 10px; }
        p { font-size: 1.2em; opacity: 0.9; }
        .badge { 
            background: #00d4aa; 
            padding: 5px 15px; 
            border-radius: 20px; 
            font-size: 0.9em;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Hello from S3!</h1>
        <p>This website is hosted on AWS S3</p>
        <p>No servers. No maintenance. Auto-scales to millions.</p>
        <span class="badge">Cost: ~₹2/month</span>
    </div>
</body>
</html>
```

**3.2 Upload via Console:**
1. **Navigate:** Your bucket → Objects tab → Upload
2. **Add files:** Select your `index.html`
3. Click **Upload**

**CLI Alternative:**
```bash
# Upload single file
aws s3 cp index.html s3://your-name-portfolio-2024/

# Upload entire folder
aws s3 sync ./my-website s3://your-name-portfolio-2024/
```

**What `s3 sync` does:** Like rsync — only uploads changed files, deletes removed files. Perfect for deployments.

**Checkpoint:** index.html visible in bucket Objects list

---

### Step 4: Configure Bucket Policy (Make Public)

1. **Navigate:** Your bucket → Permissions tab → Bucket policy → Edit
2. **Paste this policy:**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::your-name-portfolio-2024/*"
        }
    ]
}
```

3. **Replace** `your-name-portfolio-2024` with your actual bucket name
4. Click **Save changes**

**Policy Explained:**
```
"Version": "2012-10-17"     → Policy language version (always use this)
"Sid": "PublicReadGetObject" → Human-readable ID for this rule
"Effect": "Allow"           → Permission granted (not denied)
"Principal": "*"            → Who can access? Everyone (*)
"Action": "s3:GetObject"    → What can they do? Only READ files
"Resource": ".../*"         → Which files? All files in bucket
```

**Security Note:** This only allows READ. Visitors cannot upload, delete, or list your files.

**Checkpoint:** "Publicly accessible" badge appears on bucket

---

### Step 5: Test Your Website

1. **Navigate:** Bucket → Properties → Static website hosting
2. **Copy:** Bucket website endpoint URL
3. **Open in browser:** `http://your-name-portfolio-2024.s3-website.ap-south-1.amazonaws.com`

**Success Criteria:**
- [ ] Website loads without errors
- [ ] Styling displays correctly
- [ ] URL shows S3 website endpoint format

---

### Step 6: Cleanup (Optional - Keep if you want the site live)

**Delete bucket contents and bucket:**

| Step | Action | Navigation |
|------|--------|------------|
| 1 | Empty bucket | S3 → Bucket → Empty → Confirm |
| 2 | Delete bucket | S3 → Bucket → Delete → Type name → Confirm |

**CLI Cleanup:**
```bash
# Delete all objects (required before bucket deletion)
aws s3 rm s3://your-name-portfolio-2024 --recursive

# Delete bucket
aws s3 rb s3://your-name-portfolio-2024
```

**What `--recursive` does:** S3 buckets must be empty before deletion. This flag deletes all files inside first.

---

### Troubleshooting

| Problem | Cause | Solution |
|---------|-------|----------|
| 403 Forbidden | Bucket policy missing/wrong | Check Step 4; verify bucket name in policy matches exactly |
| 404 Not Found | index.html missing or wrong name | File must be named exactly `index.html` (case-sensitive) |
| "Access Denied" when saving policy | Block Public Access enabled | Bucket → Permissions → Block public access → Edit → Uncheck all |
| Website URL not working | Using wrong URL format | Use the S3 website endpoint, NOT the object URL |
| CSS/Images not loading | Files not uploaded or wrong path | Check all files uploaded; use relative paths in HTML |

### URL Formats (Common Confusion!)

| URL Type | Format | Use For |
|----------|--------|---------|
| Website endpoint | `bucket.s3-website-region.amazonaws.com` | Browsing website |
| Object URL | `bucket.s3.region.amazonaws.com/file.html` | Direct file access (won't render HTML) |

---

### Production Upgrade: Add CloudFront CDN

For real production websites, add CloudFront in front of S3:

```
Benefits:
- HTTPS support (required for modern browsers)
- Custom domain (yourname.com instead of S3 URL)
- Global edge caching (fast worldwide)
- DDoS protection (AWS Shield)
- Cost: ~$0.085/GB data transfer
```

**Architecture with CloudFront:**
```
Users → CloudFront (400+ edge locations) → S3 Bucket
         ↓
    HTTPS, caching, custom domain
```

---

### What You Learned
- Created S3 bucket with globally unique name
- Enabled static website hosting feature
- Configured bucket policy for public read access
- Understood the difference between S3 URLs and website endpoints
- Deployed a website with zero server management
- Learned when to upgrade to CloudFront for production

---


## Summary: Key Takeaways

### Session 1 Learning Outcomes

After completing this session, you should be able to:

| # | Outcome | Verified By |
|---|---------|-------------|
| 1 | Explain cloud computing and compare with on-premises | Quiz question |
| 2 | Describe load balancing algorithms and when to use each | Lab 1.11 |
| 3 | Configure Auto Scaling with target tracking policies | Lab 1.11 |
| 4 | Differentiate Edge, Fog, and Cloud computing layers | Diagram exercise |
| 5 | Deploy a static website on S3 with public access | Lab 1.12 |
| 6 | Explain DNS-based routing policies (Route 53) | Quiz question |
| 7 | Identify multi-cloud benefits and challenges | Discussion |

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

### Quick Reference: AWS CLI Commands Used

```bash
# S3 Commands
aws s3 mb s3://bucket-name              # Create bucket
aws s3 cp file.html s3://bucket/        # Upload file
aws s3 sync ./folder s3://bucket/       # Sync folder
aws s3 rm s3://bucket --recursive       # Delete all objects
aws s3 rb s3://bucket                   # Delete bucket

# EC2 / Auto Scaling Commands
aws ec2 create-launch-template          # Create launch template
aws autoscaling create-auto-scaling-group  # Create ASG
aws autoscaling put-scaling-policy      # Add scaling policy
aws autoscaling delete-auto-scaling-group --force-delete  # Cleanup

# Load Balancer Commands
aws elbv2 create-load-balancer          # Create ALB
aws elbv2 create-target-group           # Create target group
aws elbv2 delete-load-balancer          # Cleanup
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

## Preview: Why Session 2 (Security) Matters

### The Capital One Breach (2019) - A Real-World Warning

```
┌────────────────────────────────────────────────────────────────────┐
│                    WHAT HAPPENED                                   │
├────────────────────────────────────────────────────────────────────┤
│ • 100 MILLION customer records stolen                              │
│ • Credit card applications, Social Security numbers exposed        │
│ • Cost: $190 MILLION in fines and settlements                      │
└────────────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────────────┐
│                    HOW IT HAPPENED                                 │
├────────────────────────────────────────────────────────────────────┤
│ 1. EC2 instance had IAM role with TOO MUCH permission              │
│ 2. Attacker exploited a misconfigured firewall (WAF)               │
│ 3. Used EC2 metadata service to steal IAM credentials              │
│ 4. Downloaded all customer data from S3 buckets                    │
└────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────┐
│                    ROOT CAUSES                                      │
├─────────────────────────────────────────────────────────────────────┤
│ ❌❌ IAM role had "AdministratorAccess" instead of least privilege │
│ ❌❌ Security Group allowed too much inbound traffic               │
│ ❌❌ No monitoring/alerts for unusual S3 access patterns           │
│ ❌❌ EC2 metadata service (IMDSv1) was exploitable                 │
└─────────────────────────────────────────────────────────────────────┘
```

**The Scary Part:** This wasn't a sophisticated hack. It was **misconfigurations** — the same mistakes beginners make!

### What You'll Learn in Session 2

| Topic | Why It Matters |
|-------|----------------|
| **Security Groups** | One wrong 0.0.0.0/0 rule = server exposed to internet |
| **IAM** | Wrong permissions = Capital One breach |
| **Encryption** | Unencrypted S3 bucket = public data leak |
| **CloudTrail** | Know WHO did WHAT in your account |
| **GuardDuty** | AI detects hackers before damage is done |

> **WOW Moment: The Cost of Security Mistakes**
> - Average data breach cost: **$4.88 MILLION**
> - Healthcare breach: **$11.5 MILLION**
> - Capital One: **$190 MILLION** in fines
> - Equifax: **$700 MILLION** settlement
> 
> One wrong checkbox in AWS console = millions in damages.

---

## References

- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [AWS Security Best Practices](https://docs.aws.amazon.com/security/)
- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest)
- [NIST Cloud Computing Definition](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-145.pdf)
