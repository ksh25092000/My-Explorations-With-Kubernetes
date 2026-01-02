# My Explorations with Kubernetes

> A hands-on, from-scratch learning repository capturing **real understanding** of Kubernetes, Service Mesh, networking, security, and platform engineering concepts — beyond just YAMLs.



## 🎯 Purpose of this Repository

This repository documents my **deep explorations into Kubernetes and cloud-native internals**, driven by *how things actually work in production*, not just *how to deploy them*.

Instead of copy-paste manifests, this repo focuses on:

* **Why a concept exists**
* **What problem it solves**
* **How Kubernetes alone falls short**
* **How tools like Istio, CNI, CoreDNS, etc. solve it**

Think of this as **notes + experiments + mental models** for a DevOps / SRE / Platform Engineer.



## 🧠 Learning Philosophy

* 🔍 *First principles over shortcuts*
* 🧪 *Hands-on experiments, not theory dumps*
* 🧠 *Mental models, diagrams, and failure scenarios*
* 🛠 *Production-oriented thinking (SRE mindset)*



## 📚 Topics Covered (and Growing)

### 🔹 Kubernetes Core

* Kubernetes architecture (control plane vs data plane)
* Pods, Deployments, Services — beyond basics
* kube-proxy, iptables, IPVS (how traffic really flows)
* Scheduling, labels, selectors, taints & tolerations

### 🔹 Kubernetes Networking

* Pod networking fundamentals
* CNI plugins and packet flow
* DNS in Kubernetes (CoreDNS deep dive)
* North–South vs East–West traffic

### 🔹 Service Mesh (Istio)

* Why Kubernetes networking is not enough
* Control plane vs data plane (istiod vs Envoy)
* Sidecars, ingress & egress gateways
* mTLS from scratch (PeerAuthentication, DestinationRule)
* Traffic routing & splitting (VirtualService)
* Retries, timeouts, circuit breaking & outlier detection

### 🔹 Security & Zero Trust

* mTLS without app code changes
* Service identity vs IP-based security
* AuthorizationPolicy vs PeerAuthentication
* Secure service-to-service communication

### 🔹 Reliability & Resilience (SRE angle)

* Cascading failures and how to prevent them
* Retry storms and circuit breaking
* Safe deployments: canary & traffic shifting
* Failure scenarios & debugging approach



## 🧩 Structure of the Repo

```
My-Explorations-With-Kubernetes/
├── kubernetes-basics/
├── networking/
├── service-mesh-istio/
├── security/
├── reliability-patterns/
├── diagrams/
└── notes.md
```

> 📌 Structure may evolve as explorations deepen.



## 🧪 What You’ll Find Here

* ✔️ Annotated YAMLs (line-by-line explanations)
* ✔️ Diagrams for quick recall
* ✔️ Real production-style use cases
* ✔️ Common misconceptions clarified
* ✔️ Debugging commands and mental checklists



## 🚀 Who This Repo Is For

* DevOps Engineers
* SREs
* Platform Engineers
* Kubernetes learners who want **depth**, not just commands

If you’re preparing for:

* Kubernetes interviews
* Real production ownership
* SRE / Platform roles

→ This repo is for you.



## 🛠 Tools & Technologies

* Kubernetes
* Docker
* Helm / Kustomize
* Istio (Service Mesh)
* Envoy Proxy
* Linux Networking
* Cloud-native tooling



## 📌 Disclaimer

This is a **learning-first repository**.
Some experiments are simplified to explain concepts clearly before scaling them to production complexity.



⭐ If you find this useful, feel free to star the repo or fork it for your own explorations.
