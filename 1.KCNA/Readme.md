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
* Container Orchestration Fundamentals
* Runtime
* Security
* Networking
* Service Mesh
* Storage

# Cloud Native Architecture
* Cloud Native Architecture Fundamentals
* Autoscaling
* Serverless
* Community & Governance
* Personas
* Open Standards

## Cloud Native Architecture Fundamentals
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

## Autoscaling:

Autoscaling is a fundamental feature of cloud-native architecture that enables applications to automatically adjust their computing resources based on demand. Here's a clear breakdown of what it is, why it matters, and how it works in a cloud-native context:

**What is Autoscaling in Cloud-Native Architecture?**

Autoscaling refers to the automatic adjustment of resources—like virtual machines, containers, or pods—based on metrics such as CPU usage, memory consumption, queue length, or custom business metrics.

**Why Autoscaling Is Important**

**Improves Performance:** Ensures your application can handle spikes in traffic or workload.
**Reduces Cost:** Scales down resources during low demand, reducing operational costs.
**Enhances Resilience:** Maintains availability and responsiveness during failures or high loads.
**Supports Elasticity:** Key to cloud-native elasticity, where apps are dynamically responsive to demand.

### **Kubernetes Autoscaling Options:**
----
![image](https://github.com/user-attachments/assets/9430190c-7a56-4008-8913-2335639b5270)

## Serverless
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

### ✅ 1. Knative (Most Popular & Cloud-Native)
* Developed by Google, maintained by the CNCF
* Key components:
   - Knative Serving – deploy and autoscale containers (including functions)
   - Knative Eventing – bind event sources to functions
* Supports scale-to-zero and integrates with Istio or Kourier for networking

### ✅ 2. OpenFaaS
* Simpler, Docker-focused approach to FaaS on Kubernetes
* CLI and UI for deploying functions
* Supports REST, cron jobs, queues
* Easy to get started with faas-cli

### ✅ 3. Fission
* High performance, low-latency functions
* Hot reload, pre-warmed containers
* Supports multiple languages out of the box

### ✅ 4. Kubeless (Now mostly inactive)
* Kubernetes-native FaaS framework using CRDs
* Supports multiple runtimes (Python, Node.js, etc.)
* Not actively developed — consider Knative or OpenFaaS instead

### ✅ 5. KEDA (Kubernetes-based Event-Driven Autoscaler)
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

# Cloud Native Observability
* Telemetry & Observability
* Prometheus
* Cost Management

# Cloud Native Application Delivery
* Application Devivery Fundamentals
* GitOps
* CI/CD
 
