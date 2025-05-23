# KCNA - Kubernetes and Cloud Native Associate

KCNA - latest curriculum you can find here: [KCNA](https://github.com/cncf/curriculum/blob/master/KCNA_Curriculum.pdf)

 # Important Sections and Exam Coverage:

 | Section                                       |   Percentage |
 |:-----------------------------------------------|--------------|
 |Kubernetes Fundamentals                        |       46     |
 |Container Orchestration                        |       22     |
 |Cloud Native Architecture                      |       16     |
 |Cloud Native Observability                     |        8     |
 |Cloud Navtive Application Delivery             |        8     |

 # Kubernetes Fundamentals
 * Kubernetes Resources
 * Kubernetes Architecture
 * Kubernetes API
 * Containers
 * Scheduling

# Container Orchestration
1. Container Orchestration Fundamentals
2. Runtime
3. Security
4. Networking
5. Service Mesh
6. Storage

# Cloud Native Architecture
1. Cloud Native Architecture Fundamentals
2. Autoscaling
3. Serverless
4. Community & Governance
5. Personas
6. Open Standards

## 1. Cloud Native Architecture Fundamentals
**Characteristics of Cloud Native Applications:**

Cloud Native Applications harness the power of the cloud to provide increased resilience, agility, operability, and observability. Let's dive a bit deeper into these characteristics.

**Resiliency:**

Resilient applications are designed to withstand failures and continue to function or recover quickly. They typically make use of patterns such as redundancy, failover, and graceful degradation. Self-healing, where systems automatically detect and recover from failure, is a key aspect of resilient cloud-native applications. Kubernetes, for instance, has a built-in self-healing mechanism where it maintains a desired number of pod replicas and replaces failed instances.

**Agility:**

Agility in the context of cloud-native applications refers to the ability to quickly build, modify, and deploy applications. Agile practices such as microservices and continuous delivery pipelines, backed by automation, promote rapid iteration and responsiveness to change.

**Operatability:**

Operability encompasses the ease of deploying, running, and managing applications. Cloud-native applications are designed to be easily monitored, configured, and maintained. They typically leverage automation and Infrastructure as Code (IaC) tools like Terraform to streamline operations and minimise toil.

**Observability:**

Observability is the ability to understand the internal state of your system based on the outputs it generates. It's a critical component in diagnosing issues and understanding how an application behaves in the wild. Logging, monitoring, and tracing (collectively known as the 'three pillars of observability') are vital practices to understand the state and performance of cloud-native applications.

## Key Pillars of Cloud Native Architecture

Building upon the characteristics and practices discussed, cloud-native architecture is sometimes referenced as being founded on four key pillars:

**1. Microservices Architecture**

Microservices architecture involves breaking down the application into loosely coupled, independently deployable components, each focusing on a single responsibility. This design enables agility, scalability, and resilience as each microservice can be developed, scaled, and managed independently.

**2. Containerisation**

Containerisation involves encapsulating an application with its dependencies into a container, which can run uniformly across different environments. It facilitates isolation, consistency, and efficiency, making applications easier to build, deploy, and manage.

**3. DevOps**

DevOps is a collaborative approach that combines software development (Dev) and IT operations (Ops) to enhance the efficiency, reliability, and speed of software delivery. By fostering a culture of excellence, DevOps emphasises automation, monitoring, and collaboration across development and operations teams.

**4. Continuous Delivery (CD)**

Continuous Delivery is a practice where code changes are automatically built, tested, and prepared for a release to production. CD accelerates the release cycle, enhances productivity, and reduces the risk, complexity, and downtime of application deployment.

In essence, building cloud-native applications is a strategy that promotes agility, resilience, operability, and observability by leveraging modern technological practices.

With a clear understanding of these characteristics and key pillars, organisations can fully exploit the advantages of cloud-native architectures.

---

## 2. Autoscaling:

Autoscaling is a fundamental feature of cloud-native architecture that enables applications to automatically adjust their computing resources based on demand. Here's a clear breakdown of what it is, why it matters, and how it works in a cloud-native context:

**What is Autoscaling in Cloud-Native Architecture?**

Autoscaling refers to the automatic adjustment of resources—like virtual machines, containers, or pods—based on metrics such as CPU usage, memory consumption, queue length, or custom business metrics.

**Why Autoscaling Is Important**

**Improves Performance:** Ensures your application can handle spikes in traffic or workload.
**Reduces Cost:** Scales down resources during low demand, reducing operational costs.
**Enhances Resilience:** Maintains availability and responsiveness during failures or high loads.
**Supports Elasticity:** Key to cloud-native elasticity, where apps are dynamically responsive to demand.

### Kubernetes Autoscaling Options:

![image](https://github.com/user-attachments/assets/9430190c-7a56-4008-8913-2335639b5270)

---

## 3. Serverless
Serverless is a modern cloud computing model that perfectly fits within cloud-native architecture. It allows developers to build and run applications without managing servers, letting cloud providers handle infrastructure, scaling, and availability.

### ✅ What is Serverless?
In a serverless architecture, you write functions or services, and the cloud provider runs them on-demand. You are charged only for execution time, not idle time.

### 🧱 Core Characteristics of Serverless in Cloud-Native Systems:

|Feature  |Description|
|:---------|:------------|
|Event-driven|Functions are triggered by events (e.g., HTTP requests, database changes, message queues).|
|Auto-scaling|Instantly scales up/down based on demand, without manual intervention.|
|Pay-per-use|You’re billed only for function execution time and resources used.|
|Stateless|Functions are typically stateless; state is managed via databases or external storage.|
|Managed infrastructure|Developers focus on code; the provider handles servers, patching, scaling, etc.|

### 🧩 Where Serverless Fits in Cloud-Native Architecture:
Serverless is one of the pillars of cloud-native development, alongside:

* Microservices
* Containers
* DevOps & CI/CD
* APIs

#### It promotes:

🏗️ Modular applications (functions for each task)

🚀 Rapid development and deployment

🔧 Operational efficiency

###  Serverless Components in Cloud Platforms

|Cloud Provider	|Serverless Offering|
|:----------------|:--------------------|
|AWS	|AWS Lambda, API Gateway, DynamoDB|
|Azure|	Azure Functions, Logic Apps|
|Google| Cloud	Cloud Functions, Cloud Run|
|IBM Cloud|	IBM Cloud Functions (OpenWhisk)|

![image](https://github.com/user-attachments/assets/2762e7b6-0933-476b-b16f-1b33d947b29e)

Kubernetes is designed for container orchestration, but it can be extended to support serverless architectures through various open-source frameworks and tools. These solutions allow you to run functions-as-a-service (FaaS) on top of Kubernetes, blending the scalability of Kubernetes with the developer productivity of serverless.

### 🔧 Top Kubernetes Serverless Solutions:

|Framework	|Description|
|:-------|:-----|
|Knative	|Google-led project that brings serverless to Kubernetes with support for autoscaling, eventing, and HTTP-based function invocation.|
|Kubeless	|Lightweight FaaS framework built on top of Kubernetes Custom Resource Definitions (CRDs).|
|OpenFaaS	|Function-as-a-Service on Kubernetes with easy deployment and built-in monitoring.|
|Fission	|Fast serverless functions for Kubernetes, focused on developer speed and performance.|
|KEDA	|Kubernetes-based Event-Driven Autoscaler, often used with Knative or custom workloads for event-driven scaling.|

### ✅ i. Knative (Most Popular & Cloud-Native)
* Developed by Google, maintained by the CNCF
* Key components:
   - Knative Serving – deploy and autoscale containers (including functions)
   - Knative Eventing – bind event sources to functions
* Supports scale-to-zero and integrates with Istio or Kourier for networking

### ✅ ii. OpenFaaS
* Simpler, Docker-focused approach to FaaS on Kubernetes
* CLI and UI for deploying functions
* Supports REST, cron jobs, queues
* Easy to get started with faas-cli

### ✅ iii. Fission
* High performance, low-latency functions
* Hot reload, pre-warmed containers
* Supports multiple languages out of the box

### ✅ iv. Kubeless (Now mostly inactive)
* Kubernetes-native FaaS framework using CRDs
* Supports multiple runtimes (Python, Node.js, etc.)
* Not actively developed — consider Knative or OpenFaaS instead

### ✅ v. KEDA (Kubernetes-based Event-Driven Autoscaler)
* Works with any Kubernetes deployment (not just functions)
* Event-driven autoscaling for queues, Kafka, Prometheus, etc.
* Often used with Knative or Azure Functions on Kubernetes

### 🚀 When to Use Serverless on Kubernetes
Use Kubernetes serverless when:
- You already have a Kubernetes platform (e.g., EKS, GKE, AKS).
- You want more control than public FaaS (e.g., AWS Lambda) offers.
- You need to run event-driven apps alongside microservices.

### 🛠️ Recommendation
|Use Case	|Best Solution|
|:---|:----|
|Enterprise-ready, event-driven apps	|Knative + KEDA|
|Simple, DIY serverless	|OpenFaaS|
|Low-latency function execution	|Fission|

## 4. 🌍 Community and Governance of Cloud-Native

The cloud-native ecosystem is driven by a vibrant open-source community and structured governance to ensure collaboration, innovation, and interoperability. At the center of this ecosystem is the Cloud Native Computing Foundation (CNCF) — the primary organization overseeing the development and direction of cloud-native technologies.

### 🏛️ 1. Cloud Native Computing Foundation (CNCF)
#### ✅ What is CNCF?
The CNCF is a vendor-neutral, open-source foundation under the Linux Foundation that promotes the adoption of cloud-native computing. It hosts and governs many of the most critical cloud-native projects.

#### 🔑 CNCF’s Mission:
```**“Make cloud-native computing ubiquitous.”**```
##### 🔍 What This Means
The CNCF works to:
* Support and sustain open-source projects that enable cloud-native development.
* Foster collaboration between developers, vendors, and end users.
* Define standards and best practices for modern, scalable, and resilient cloud-native systems.
* Educate and promote adoption of technologies like containers, microservices, orchestration, and observability tools.

#### CNCF Maintains Projects Like:
|Project	|Description|
|:----|:-----|
|Kubernetes|	Container orchestration|
|Prometheus|	Monitoring and alerting|
|Envoy|	High-performance service proxy|
|gRPC|	High-performance RPC framework|
|Helm|	Kubernetes package manager|
|OpenTelemetry|	Observability and tracing|

### 🧩 2. Governance Model
CNCF’s governance ensures transparency and community involvement. It includes:

|Governance Body|	Role|
|:-------|:----------|
|TOC (Technical Oversight Committee)|	Guides technical vision, accepts or retires projects.|
|Governing Board	|Business and strategic direction, financial oversight.|
|End User Community|	Large-scale adopters (e.g., Apple, Spotify, Intuit) provide feedback and shape priorities.|
|SIGs (Special Interest Groups)|	Focused on specific areas like observability, storage, security.|
|Working Groups	|Solve cross-project or community-wide challenges.|

```✅ All meetings, proposals, and votes are public and transparent, often on GitHub or in public Zoom calls.``` 

### 🌐 3. Community Involvement
|Channel	|How the Community Engages|
|:-----|:----|
|GitHub	|Open-source contributions to CNCF projects.|
|Slack (CNCF workspace)	|Discussions, Q&A, collaboration.|
|CNCF Events (e.g., KubeCon)|	Global and regional conferences.|
|Mentorship Programs|	Google Summer of Code, LFX Mentorship.|
|Technical Blogs & Docs	|Created and maintained by community members.|

```🔗 Join CNCF Slack: https://slack.cncf.io/```

### 💼 4. Membership Tiers
The CNCF ecosystem includes:
* End Users (e.g., Box, Spotify, JPMorgan)
* Vendors (e.g., AWS, Microsoft, Google, Red Hat)
* Startups and Academia
  
These members help fund the CNCF, contribute engineering resources, and drive adoption of standards and best practices.


### 📈 5. Maturity Model for Projects

|Various graphs that shows the maturity levels| |
|----|---|
|![image](https://github.com/user-attachments/assets/71533d1c-3fb3-424b-9efb-17cfdf747e90)|![image](https://github.com/user-attachments/assets/5f3964b5-9eb0-4cde-9553-bf36332de5de)|

CNCF classifies projects into:
|Level|	Description|
|:----|:-----|
|Sandbox	|Early-stage, experimental projects.|
|Incubating	|Growing adoption and governance maturity.|
|Graduated	|Widely adopted, production-ready, and stable.|

Graduated projects include Kubernetes, Prometheus, Envoy, Helm, etc.

## 5. Personas:

In the context of the Cloud Native Computing Foundation (CNCF), the term "**personas**" refers to the various roles and stakeholders involved in the development, deployment, and operation of cloud-native applications. These personas help in understanding the responsibilities, challenges, and perspectives of different individuals or groups within the cloud-native ecosystem.

![image](https://github.com/user-attachments/assets/b52a659e-db2f-4bf1-a543-0b3251d9b389)

### Key Personas in Cloud-Native Computing:

||Role|Responsibilities|Example| 
|:--|:--|:--|:--|
|**Infrastructure Operator**|Manages the physical and virtual infrastructure, including hardware, firmware, and cloud resources|- Ensures the availability and reliability of the underlying infrastructure. <br> - Applies firmware updates and manages hardware configurations. <br> - Oversees the provisioning of compute, storage, and networking resources.| Cloud Service Providers (CSPs), Site Reliability Engineers (SREs).|
|**Orchestration Operator**|Handles the deployment and management of workloads within orchestration platforms like Kubernetes.| - Deploys and manages containerized applications. <br> - Monitors and scales workloads based on demand. <br> - Manages cluster resources and services.|Kubernetes Administrators, DevOps Engineers.|
|**Container Image Provider**|Develops and distributes container images used in cloud-native applications.|- Builds and maintains container images. <br> - Signs and secures images to ensure integrity. <br> - Provides mechanisms for verifying image authenticity.|Software Vendors, Independent Developers.|
|**Data Owner**|Owns and governs the data utilized by cloud-native applications.|- Ensures data privacy and compliance with regulations. <br> - Monitors data access and usage. <br> - Collaborates with other personas to secure data.|Compliance Officers, Data Stewards.|
|**Application Developer**|Designs and develops applications tailored for cloud-native environments.|- Writes microservices and APIs. <br> - Integrates with cloud-native services and platforms. <br> - Ensures applications are scalable and resilient.|Software Engineers, Full-Stack Developers.|
|**Security Specialist**|Focuses on securing cloud-native applications and infrastructure.|- Identifies and mitigates security vulnerabilities. <br> - Implements security best practices and policies. <br> - Conducts audits and compliance checks.|Security Engineers, Cloud Security Architects.|

#### Importance of Personas
Understanding these personas is crucial for several reasons:

* **Tailored Solutions:** Designing tools and platforms that cater to the specific needs and challenges of each persona.
* **Effective Communication:** Facilitating better collaboration and communication among different stakeholders.
* **Security and Compliance:** Ensuring that all roles are aware of and adhere to security and compliance requirements.
* **Optimized Operations:** Streamlining processes and workflows by aligning them with the responsibilities of each persona.

|DevOps | Roles| Life Cycle|
|-----|-----|---|
|![image](https://github.com/user-attachments/assets/f2028904-b471-43d1-8263-7c81e51fa10d)|![image](https://github.com/user-attachments/assets/ccb2d818-5ee5-444e-8e3a-3ccb2a15276b)|![image](https://github.com/user-attachments/assets/4f9d641a-b83a-4d35-99b8-a55e2fb4b98d)|

|SRE| Roles|Special Focus|
|--|--|--|
|![image](https://github.com/user-attachments/assets/da95e899-14cf-4351-988c-bf35ba2e7a2a)|![image](https://github.com/user-attachments/assets/a6d8ce1e-32bf-45ab-81f2-4a707cc7ab98)|![image](https://github.com/user-attachments/assets/2f77acea-726b-4137-87e4-a1bbdbf23e1a)|

|CloudOps Engineer Skill Set & Roles|Security Engineer & Roles|DevSecOps Engineer|
|--|--|--|
|![image](https://github.com/user-attachments/assets/c9e2ebe2-80ce-42f1-8cc3-e697bbb545b0)|![image](https://github.com/user-attachments/assets/cbf8d821-c393-403d-927f-69712fd80485)|![image](https://github.com/user-attachments/assets/cff9f890-ec16-4897-b453-1d6634d77234)|

|Full Stack Developer & Roles|Cloud Architect & Roles |Data Engineer & Roles|
|---|---|---|
|![image](https://github.com/user-attachments/assets/ac286904-edb8-4705-88dd-66e098107d23)|![image](https://github.com/user-attachments/assets/37800e51-945a-4f4d-a4c2-7d13997c9182)|![image](https://github.com/user-attachments/assets/b21eca20-41c6-439f-b2ad-260e50e4be47)|



















----
# Cloud Native Observability
* Telemetry & Observability
* Prometheus
* Cost Management

# Cloud Native Application Delivery
* Application Devivery Fundamentals
* GitOps
* CI/CD
 
