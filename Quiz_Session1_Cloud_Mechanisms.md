# Quiz: Session 1 - Cloud Management Mechanisms
## Resource & Architecture Mechanisms (Discussion Version)

**Total Questions:** 25  
**Time:** 30 minutes  
**Difficulty:** Easy to Moderate

---

### Section A: Cloud Fundamentals (Q1-5)

**Q1.** What is the primary pricing model of cloud computing?
- A) One-time purchase
- B) Annual subscription only
- C) Pay-per-use
- D) Free forever

**Answer:** C

**Explanation:** Cloud computing's core value proposition is pay-per-use (also called pay-as-you-go). You only pay for the compute, storage, and bandwidth you actually consume. This is different from traditional IT where you buy hardware upfront. Example: EC2 charges per hour/second of usage.

---

**Q2.** A startup wants to launch their app globally without building data centers. Which cloud benefit helps them most?
- A) Cost savings
- B) Global deployment
- C) Maintenance reduction
- D) Security

**Answer:** B

**Explanation:** Cloud providers like AWS have 30+ regions worldwide. A startup can deploy their app in Mumbai, Singapore, and US in minutes without building physical infrastructure. This "global deployment" capability would cost millions and take years to build on-premises.

---

**Q3.** Match the service model: "You upload code, platform handles servers, scaling, and runtime."
- A) IaaS
- B) PaaS
- C) SaaS
- D) FaaS

**Answer:** B

**Explanation:** 
- **IaaS** (EC2): You manage OS, runtime, code
- **PaaS** (Heroku, Elastic Beanstalk): You manage only code, platform handles rest
- **SaaS** (Gmail): You just use the application
- **FaaS** (Lambda): Function-level, even more abstracted than PaaS

---

**Q4.** Netflix uses AWS instead of building their own data centers. What type of expense did they convert?
- A) OpEx to CapEx
- B) CapEx to OpEx
- C) Fixed to Variable
- D) Both B and C

**Answer:** D

**Explanation:** 
- **CapEx → OpEx**: Instead of buying servers (capital expense), Netflix pays monthly AWS bills (operational expense)
- **Fixed → Variable**: Instead of fixed costs for hardware, costs now vary with actual usage
Both transformations happen when moving to cloud.

---

**Q5.** A company needs AI/ML capabilities AND Microsoft Active Directory integration. What's the best approach?
- A) Use only AWS
- B) Use only Azure
- C) Use GCP for AI/ML, Azure for AD integration
- D) Build your own data center

**Answer:** C

**Explanation:** This is a multi-cloud use case. GCP excels in AI/ML (TensorFlow, BigQuery ML), while Azure has native Active Directory integration. Using each provider's strength is a valid multi-cloud strategy. No single provider is best at everything.

---

### Section B: Load Balancing (Q6-10)

**Q6.** Flipkart receives 10 million requests during Big Billion Days. Without load balancing, what would happen?
- A) Faster response times
- B) Server overload and crashes
- C) Automatic scaling
- D) Better security

**Answer:** B

**Explanation:** A single server can handle ~1000-10000 requests/second. 10 million requests hitting one server = instant crash. Load balancer distributes these across hundreds of servers, each handling a manageable portion. Without it, the server would be overwhelmed.

---

**Q7.** **Scenario:** An e-commerce site uses shopping carts stored in server memory. If a user is sent to a different server mid-session, their cart disappears. Which algorithm prevents this?
- A) Round Robin
- B) Least Connections
- C) IP Hash (sticky sessions)
- D) Random

**Answer:** C

**Explanation:** IP Hash uses the client's IP address to determine which server to route to. Same IP = same server every time. This "sticky session" ensures a user stays on the same server throughout their session, preserving in-memory data like shopping carts.

---

**Q8.** **Story:** During Diwali sale, Myntra's website shows "Please wait, you're in queue" instead of crashing. This is because:
- A) They turned off their servers
- B) Load balancer is managing traffic overflow
- C) Users have slow internet
- D) The website is under maintenance

**Answer:** B

**Explanation:** The queue system is a load balancer feature called "request queuing" or "rate limiting." Instead of overwhelming servers and crashing, excess requests are held in a queue and processed when capacity is available. Better user experience than a crash.

---

**Q9.** **Scenario:** You want your load balancer to route `/api/*` requests to backend servers and `/images/*` to a CDN. This requires URL path inspection. Which load balancer type can do this?
- A) Network Load Balancer (Layer 4)
- B) Application Load Balancer (Layer 7)
- C) Classic Load Balancer
- D) DNS-based routing

**Answer:** B

**Explanation:** 
- **Layer 4 (NLB)**: Can only see IP + port, cannot inspect URL paths
- **Layer 7 (ALB)**: Can inspect HTTP headers, URL paths, cookies
Path-based routing (`/api/*` vs `/images/*`) requires understanding HTTP = Layer 7 = ALB.

---

**Q10.** A health check fails for Server-2 in a load balancer pool. What happens next?
- A) All servers stop
- B) Traffic continues to Server-2
- C) Traffic is routed only to healthy servers
- D) Load balancer restarts

**Answer:** C

**Explanation:** Load balancers continuously ping servers (health checks). If a server fails to respond, it's marked "unhealthy" and removed from the pool. Traffic is automatically routed to remaining healthy servers. This is how high availability works.

---

### Section C: Auto-Scaling (Q11-15)

**Q11.** **Scenario:** Hotstar streams IPL final. Viewership jumps from 1 million to 50 million in 30 minutes. Which mechanism handles this?
- A) Load Balancing
- B) Auto-Scaling
- C) Edge Computing
- D) Multi-Cloud

**Answer:** B

**Explanation:** Auto-scaling automatically adds servers when demand increases. 1M → 50M viewers means ~50x more servers needed. Auto-scaling detects high CPU/traffic and launches new instances automatically. Load balancing distributes traffic but doesn't add capacity.

---

**Q12.** What is the difference between horizontal and vertical scaling?
- A) Horizontal adds more servers; Vertical upgrades existing server
- B) Horizontal upgrades servers; Vertical adds more servers
- C) Both are the same
- D) Horizontal is for storage; Vertical is for compute

**Answer:** A

**Explanation:** 
- **Horizontal (Scale Out)**: Add more servers (2 → 10 servers)
- **Vertical (Scale Up)**: Upgrade server size (t2.micro → t2.xlarge)
Horizontal is preferred because: no single point of failure, theoretically unlimited capacity.

---

**Q13.** **Scenario:** Swiggy notices order volume doubles every day at lunch (12-2 PM) and dinner (7-10 PM). To optimize costs, they should use:
- A) Fixed 100 servers running 24/7
- B) Scheduled auto-scaling for peak hours
- C) Manual server starts twice a day
- D) No servers during off-peak

**Answer:** B

**Explanation:** Predictable patterns = scheduled scaling. Configure: "Scale to 50 servers at 11:45 AM, scale down to 10 servers at 2:30 PM." No manual intervention, no paying for unused capacity during off-peak hours.

---

**Q14.** **Scenario:** Your team says "We want servers to automatically adjust so CPU stays around 70% - not too idle, not overloaded." Which scaling approach fits?
- A) Add 2 servers when CPU > 80% (step-based)
- B) Maintain CPU at 70% automatically (target tracking)
- C) Scale up every day at 9 AM (scheduled)
- D) Manually add servers when needed

**Answer:** B

**Explanation:** Target Tracking automatically adjusts capacity to maintain a metric at a target value. "Keep CPU at 70%" = target tracking. It continuously monitors and adds/removes servers to maintain that target. More intelligent than step scaling.

---

**Q15.** A company runs 10 servers 24/7 for peak load that occurs only 2 hours/day. With auto-scaling, they could save approximately:
- A) 10-20%
- B) 40-70%
- C) 90-100%
- D) No savings

**Answer:** B

**Explanation:** If peak = 2 hours/day, they need 10 servers only 8% of the time. Other 92% they could run 2 servers (baseline). Savings: running 2 servers instead of 10 for 22 hours = ~80% reduction during off-peak. Typical savings: 40-70% depending on workload patterns.

---

### Section D: Multi-Cloud & Hypervisor (Q16-18)

**Q16.** **Use Case:** RBI mandates that Indian banks cannot depend on a single cloud provider. Which architecture addresses this?
- A) Hybrid Cloud
- B) Multi-Cloud
- C) Private Cloud
- D) Edge Cloud

**Answer:** B

**Explanation:** Multi-cloud = using multiple cloud providers (AWS + Azure + GCP). This addresses regulatory requirements for no single vendor dependency. If one provider has an outage, others continue working. Different from hybrid cloud (on-prem + cloud).

---

**Q17.** **Scenario:** AWS experiences a major outage in 2025 affecting thousands of businesses. Which companies would be LEAST affected?
- A) Companies using only AWS
- B) Companies using AWS + Azure + GCP
- C) Companies using AWS in multiple regions
- D) Companies with no backup plan

**Answer:** B

**Explanation:** Multi-cloud (AWS + Azure + GCP) provides protection against entire provider outages. Multi-region (option C) only helps with regional outages within AWS. When AWS itself is down (API issues, IAM failures), multi-region within AWS doesn't help.

---

**Q18.** **Scenario:** VMware ESXi runs directly on server hardware, while VirtualBox runs on top of Windows/Linux. ESXi is which type?
- A) Type 1 (Bare Metal) - better performance
- B) Type 2 (Hosted) - easier setup
- C) Container-based
- D) Cloud-native

**Answer:** A

**Explanation:** 
- **Type 1 (Bare Metal)**: Hypervisor runs directly on hardware (ESXi, Xen, Hyper-V). Used in production for better performance.
- **Type 2 (Hosted)**: Hypervisor runs on top of an OS (VirtualBox, VMware Workstation). Used for development/testing.

---

### Section E: DNS Routing & Cloud Balancing (Q19-21)

**Q19.** **Scenario:** A user in Mumbai gets 20ms response from your app, while a user in New York gets 180ms. Your DNS automatically routes users to the nearest server. This is:
- A) Random routing
- B) Round-robin routing
- C) Latency-based routing
- D) Alphabetical routing

**Answer:** C

**Explanation:** Latency-based routing measures actual latency from user to each region and routes to the lowest-latency destination. Mumbai user → Mumbai servers (20ms), NY user → US-East servers. AWS Route 53 supports this natively.

---

**Q20.** A company wants to send 80% traffic to new servers and 20% to old servers for A/B testing. Which routing policy?
- A) Failover
- B) Weighted
- C) Latency
- D) Multivalue

**Answer:** B

**Explanation:** Weighted routing sends traffic based on assigned weights. Set new servers = weight 80, old servers = weight 20. Used for: A/B testing, gradual migrations (blue-green deployment), canary releases.

---

**Q21.** **Scenario:** Your primary servers in Mumbai go down. You need traffic to automatically switch to backup servers in Singapore. Which DNS routing achieves this?
- A) Weighted (splits traffic by percentage)
- B) Failover (active-passive switch)
- C) Latency (routes to fastest)
- D) Simple (single destination)

**Answer:** B

**Explanation:** Failover routing = active-passive disaster recovery. Primary (Mumbai) is active, Secondary (Singapore) is passive. DNS health checks monitor Mumbai; if it fails, automatically switches to Singapore. Used for disaster recovery scenarios.

---

### Section F: Edge & Fog Computing (Q22-24)

**Q22.** **Scenario:** A self-driving car needs to make a "STOP" decision. Why can't it rely on cloud processing?
- A) Cloud is too expensive
- B) Cloud latency (100-200ms) is too slow for safety
- C) Cloud doesn't support AI
- D) Cars don't have internet

**Answer:** B

**Explanation:** At 60 mph, a car travels 2.7 meters in 100ms. If "STOP" decision takes 200ms cloud round-trip, the car travels 5+ meters before reacting. This delay could be fatal. Edge AI processes locally in <10ms for safety-critical decisions.

---

**Q23.** **Scenario:** PUBG Mobile processes bullet hit detection. If this was done in cloud (200ms away), what would happen?
- A) Better graphics
- B) Player shoots, enemy dies 200ms later = unfair gameplay
- C) Game runs faster
- D) No difference

**Answer:** B

**Explanation:** 200ms delay in competitive gaming is unacceptable. You shoot, but hit registers 200ms later = enemy has unfair advantage. Games process physics/hit detection locally or on edge servers (<50ms latency). Cloud is too slow for real-time gaming.

---

**Q24.** A smart factory has 10,000 sensors generating 1TB/hour of data. A local gateway processes this and sends only 1GB/hour of aggregated data to cloud. This gateway represents:
- A) Edge Computing
- B) Fog Computing
- C) Cloud Computing
- D) Serverless Computing

**Answer:** B

**Explanation:** 
- **Edge**: Processing on the device itself
- **Fog**: Intermediate layer (gateway) that aggregates and processes data from multiple edge devices before sending to cloud
- **Cloud**: Central processing
The gateway aggregating 10,000 sensors = Fog computing layer.

---

### Section G: S3 Static Website (Q25)

**Q25.** **Use Case:** A student wants to host their portfolio website (HTML/CSS/JS) with zero server management and near-zero cost. Best AWS service?
- A) EC2 with Apache
- B) S3 Static Website Hosting
- C) AWS Lambda
- D) Amazon Lightsail

**Answer:** B

**Explanation:** S3 Static Website Hosting:
- No servers to manage (serverless)
- Costs ~$0.02/month for small sites
- Auto-scales to millions of visitors
- Perfect for static content (HTML/CSS/JS)
EC2 requires server management; Lambda is for backend code, not hosting files.

---

## Answer Key

| Q | Answer | Q | Answer | Q | Answer | Q | Answer | Q | Answer |
|---|--------|---|--------|---|--------|---|--------|---|--------|
| 1 | C | 6 | B | 11 | B | 16 | B | 21 | B |
| 2 | B | 7 | C | 12 | A | 17 | B | 22 | B |
| 3 | B | 8 | B | 13 | B | 18 | A | 23 | B |
| 4 | D | 9 | B | 14 | B | 19 | C | 24 | B |
| 5 | C | 10 | C | 15 | B | 20 | B | 25 | B |

---
*Session 1 Quiz (Discussion Version) | Cloud Computing Workshop*
