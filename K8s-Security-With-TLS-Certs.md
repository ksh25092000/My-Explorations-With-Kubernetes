# 🔐 Securing Kubernetes with TLS Certificates (From First Principles)

This document explains **how TLS and certificates secure Kubernetes**, from *who talks to whom* to *which certificates are used*, with a **clear mental model suitable for GitHub notes, interviews, and CKA-level understanding**.



## 1. Why Kubernetes Needs Certificates

Kubernetes is a **distributed system** with many components communicating over the network.

Security goals:

* 🔒 Encryption in transit
* 🧾 Strong authentication (who is calling?)
* 🛡️ Protection against MITM attacks

Kubernetes achieves this using **TLS certificates**.



## 2. Types of Certificates in Kubernetes

Kubernetes mainly uses **three types of certificates**.

### 2.1 Server (Serving) Certificates

Used by components that **expose an API endpoint** and wait for incoming connections.

Examples:

* kube-apiserver
* etcd
* kubelet (on worker nodes)

📌 Purpose:

* Prove the server’s identity
* Enable HTTPS / encrypted communication



### 2.2 Client Certificates

Used by users or components that **initiate a connection** to a server.

Examples:

* kubectl (admin)
* scheduler
* controller-manager
* kube-proxy
* kubelet (when talking to API server)

📌 Purpose:

* Authenticate the caller to the API server



### 2.3 CA (Certificate Authority) Certificates

Used to **sign and validate** all server and client certificates.

* `ca.crt` → public cert (used to verify)
* `ca.key` → private key (used to sign)

📌 Trust in Kubernetes is rooted in the CA.



## 3. Certificate Naming Conventions (Practical Tip)

* Public certificates → `.crt` or `.pem`
* Private keys → `.key` or `-key.pem`



## 4. What Is a “Server” in Kubernetes?

A component is considered a **server** if it:

1. **Listens** on a network port
2. **Authenticates** clients (certs / tokens)
3. **Responds** with a defined API (REST / gRPC)

### Server Components & APIs

| Component      | API Exposed   | Port      | Primary Client       |
| -- | - |  | -- |
| kube-apiserver | REST          | 6443      | kubectl, controllers |
| etcd           | gRPC          | 2379/2380 | kube-apiserver       |
| kubelet        | HTTPS         | 10250     | kube-apiserver       |
| containerd     | gRPC (socket) | unix      | kubelet              |
| CoreDNS        | DNS           | 53        | Pods                 |



## 5. Server Certificates in Kubernetes

### Example: kube-apiserver

When a client connects, it must verify **it is really talking to the API server**.

The API server presents a **server certificate**:

* `apiserver.crt`
* `apiserver.key`

📌 Purpose:

* Prevent MITM attacks
* Encrypt all API traffic

📂 Typical location:

```
/etc/kubernetes/pki/apiserver.crt
/etc/kubernetes/pki/apiserver.key
```



## 6. Client Certificates in Kubernetes

Client certificates are presented **by callers** to authenticate themselves.

### Common Clients

* kubectl (admin user)
* scheduler
* controller-manager
* kube-proxy
* kubelet

The API server verifies:

* Certificate is signed by trusted CA
* CN / Organization matches RBAC rules



### kubectl (Admin) Example

`$HOME/.kube/config` contains:

```yaml
client-certificate-data: <base64>
client-key-data: <base64>
```

Or references:

```yaml
client-certificate: /etc/kubernetes/pki/admin.crt
client-key: /etc/kubernetes/pki/admin.key
```



## 7. Client vs Server: Real-Life Analogy

| Certificate Type | Analogy           | Used By       | Verified By |
| - | -- | - | -- |
| Server Cert      | Server’s ID card  | API server    | Clients     |
| Client Cert      | Login credentials | Users / nodes | API server  |



## 8. Who Signs All These Certificates?

All certificates are signed by the **cluster CA**.

📂 Default CA location:

```
/etc/kubernetes/pki/ca.crt
/etc/kubernetes/pki/ca.key
```

📌 You may also have a **separate CA for etcd**.



## 9. Component-by-Component Certificate Usage

### kube-apiserver

* **Server cert**: `apiserver.crt`
* **Client certs**:

  * To etcd → `apiserver-etcd-client.crt`
  * To kubelet → `apiserver-kubelet-client.crt`

📌 The API server is **both a server and a client**.



### etcd

* Server cert: `etcdserver.crt`
* Peer cert: `peer.crt`
* CA: `etcd/ca.crt`

Validates:

* API server client certificate



### kubelet

Roles:

* **Server** → exposes HTTPS API (logs/exec)
* **Client** → talks to API server & container runtime

Certificates:

* Server: `kubelet.crt`
* Client: `kubelet-client-*.pem`



### kube-scheduler & controller-manager

* Authenticate to API server using client certs

```text
scheduler.crt + scheduler.key
controller-manager.crt + controller-manager.key
```



### kube-proxy

Primarily a **client**:

* Watches API server for Services & Endpoints

Also exposes:

* Small health endpoint (e.g., :10256)



## 10. Communication Matrix (Mental Model)

| Server         | Clients              | Certificates Required           |
| -- | -- | - |
| kube-apiserver | kubectl, controllers | Server + client cert validation |
| etcd           | kube-apiserver       | etcd server cert                |
| kubelet        | kube-apiserver       | kubelet server cert             |



## 11. Folder Structure (Typical)

```
/etc/kubernetes/pki/
├── apiserver.crt
├── apiserver.key
├── apiserver-etcd-client.crt
├── apiserver-kubelet-client.crt
├── admin.crt
├── admin.key
├── ca.crt
├── ca.key
├── etcd/
│   ├── ca.crt
│   ├── server.crt
│   └── peer.crt
```



## 12. Cheat Sheet (High-Value for CKA)

| Component        | Cert Path                         | Purpose        |
| - |  | -- |
| kube-apiserver   | /etc/kubernetes/pki/apiserver.crt | API server TLS |
| apiserver → etcd | apiserver-etcd-client.crt         | Auth to etcd   |
| admin (kubectl)  | admin.crt                         | Human access   |
| kubelet server   | /var/lib/kubelet/pki/kubelet.crt  | Logs/exec      |
| kubelet client   | kubelet-client-*.pem              | Auth to API    |



## 13. Interview Gold Answers

### Q: Is kube-proxy a client or server?

**Answer:**

> Primarily a client to the API server, but it also exposes a small health-check server for load balancers.



### Q: Is the API server only a REST server?

**Answer:**

> No. It is a REST server for the cluster, a gRPC client to etcd, and an HTTPS client to kubelets. This bidirectional communication keeps control plane and nodes in sync.



## 14. Key Takeaways

* Kubernetes security is **certificate-driven**
* Every connection has **clear client and server roles**
* CA is the root of trust
* Many components act as **both client and server**



📌 These concepts are **critical for troubleshooting TLS issues**, certificate rotation, and CKA/CKS exams.
