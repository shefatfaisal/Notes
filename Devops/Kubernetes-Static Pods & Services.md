

### Complete Visual Study Notes

---

## Table of Contents

1. [Static Pods](https://claude.ai/new?incognito#1-static-pods)
2. [Kubernetes Services Overview](https://claude.ai/new?incognito#2-kubernetes-services-overview)
3. [ClusterIP](https://claude.ai/new?incognito#3-clusterip)
4. [NodePort](https://claude.ai/new?incognito#4-nodeport)
5. [LoadBalancer](https://claude.ai/new?incognito#5-loadbalancer)
6. [Services Quick Comparison](https://claude.ai/new?incognito#6-services-quick-comparison)

---

# 1. Static Pods

## What is a Static Pod?

A **Static Pod** is a pod that is managed **directly by the Kubelet** on a specific node — completely bypassing the API Server and control plane.

> 💡 Normal pods are created via `kubectl` → API Server → Scheduler → Kubelet. Static pods skip all of that — Kubelet manages them **directly**.

---

## How Static Pods Work

```
Normal Pod Flow:
kubectl apply → API Server → Scheduler → Kubelet → Pod

Static Pod Flow:
YAML file dropped in /etc/kubernetes/manifests/ → Kubelet → Pod ✅
(No kubectl needed, no API server, no scheduler)
```

---

## The Magic Directory

```
/etc/kubernetes/manifests/
```

- Kubelet **watches this directory** on the node
- Any YAML file placed here → Kubelet **automatically creates** the Pod
- Any YAML file removed → Kubelet **automatically deletes** the Pod
- No `kubectl create` needed — ever!

---

## Mirror Pod Concept

When a static pod runs, the Kubelet creates a **"mirror pod"** in the API server so you can **see** it with `kubectl get pods`.

```
Node (Worker)                        Control Plane
┌─────────────────────┐              ┌─────────────────────┐
│                     │              │                     │
│  /etc/kubernetes/   │              │    API Server       │
│    manifests/       │              │                     │
│   static-nginx.yml  │──creates──▶  │  mirror pod         │
│         ↓           │   mirror     │  (read-only copy)   │
│      Kubelet        │              │                     │
│         ↓           │              └─────────────────────┘
│    Static Pod ✅    │
│                     │
└─────────────────────┘
```

### Mirror Pod Rules:

- ✅ You CAN see it with `kubectl get pods`
- ❌ You CANNOT edit it via kubectl
- ❌ You CANNOT delete it via kubectl (it comes back automatically!)

---

## Static Pod vs Normal Pod

|Feature|Static Pod|Normal Pod|
|---|---|---|
|Managed by|**Kubelet** directly|**Kube-API Server**|
|Created via|YAML in `/etc/kubernetes/manifests/`|`kubectl apply/create`|
|Scheduler involved?|❌ No|✅ Yes|
|Runs on|That specific node only|Any node (scheduler decides)|
|ReplicaSet/Deployment?|❌ No|✅ Yes|
|Visible in kubectl?|✅ Yes (mirror pod)|✅ Yes|
|Deletable via kubectl?|❌ No (comes back!)|✅ Yes|
|Use case|System components, node-local apps|User workloads|

---

## Static Pod Flow (Step by Step)

```
1️⃣  Drop YAML file in /etc/kubernetes/manifests/
            ↓
2️⃣  Kubelet detects the file automatically
            ↓
3️⃣  Kubelet creates the Pod directly on that node
            ↓
4️⃣  API Server sees it → creates "mirror pod" for visibility
            ↓
5️⃣  If you delete the mirror pod via kubectl → Pod keeps running!
            ↓
6️⃣  Only way to delete → remove the YAML file from the node
```

---

## Lab: Create a Static Pod

### Step 1 — SSH into the Worker Node

```bash
# From kops machine, SSH into the worker node
ssh -i "kubernetes.reyaz.k8s.local-1d:84:ff.pem" ubuntu@ec2-13-234-78-82.ap-south-1.compute.amazonaws.com
```

### Step 2 — Navigate to Manifests Directory

```bash
cd /etc/kubernetes/manifests/
```

### Step 3 — Create the Static Pod YAML

```bash
vi static-nginx.yml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: static-nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    resources:
      requests:
        cpu: "100m"       # Minimum CPU needed
        memory: "128Mi"   # Minimum memory needed
      limits:
        cpu: "200m"       # Maximum CPU allowed
        memory: "256Mi"   # Maximum memory allowed
```

> ✅ No `kubectl create` needed! Just saving the file is enough. Kubelet will create the pod automatically.

### Step 4 — Verify from Control Plane

```bash
# Go back to kops/control plane machine
kubectl get pods
# You'll see: static-nginx-<nodeName>  ← mirror pod
```

### Step 5 — Try to Delete (It Comes Back!)

```bash
kubectl delete pod static-nginx-<nodeName>
# Pod gets deleted... but comes right back! 🔄
# Because the YAML file is still in /etc/kubernetes/manifests/
```

### Step 6 — Actually Delete (Remove the File)

```bash
# SSH back to the worker node
cd /etc/kubernetes/manifests/
rm static-nginx.yml
# Now the pod is truly gone ✅
```

---

## Control Plane Uses Static Pods!

The Kubernetes control plane itself runs as static pods:

```bash
kubectl get pods -n kube-system -o wide
```

You'll see pods like:

- `kube-apiserver-<node>`
- `etcd-<node>`
- `kube-controller-manager-<node>`
- `kube-scheduler-<node>`

These are all **static pods** running from `/etc/kubernetes/manifests/` on the control plane node — that's how Kubernetes bootstraps itself!

---

---

# 2. Kubernetes Services Overview

## Why Services?

Pods are **temporary** — they get created, deleted, rescheduled, and their IP addresses change constantly. If you connect directly to a pod IP, you'll lose connection every time a pod restarts.

**Services solve this by providing:**

- A **stable IP address** that never changes
- A **stable DNS name** that never changes
- **Load balancing** across all matching pods automatically

```
Without Service:                    With Service:
                                    
Pod A → IP: 10.0.0.1 (changes!)   Client → my-service:80 (stable!)
Pod B → IP: 10.0.0.2 (changes!)        ↓
Pod C → IP: 10.0.0.3 (changes!)   Service (stable IP)
                                        ↓
                                   Pods (IPs can change freely)
```

---

## How Services Find Pods — Labels & Selectors

Services use **labels** to find pods. Any pod with a matching label gets traffic:

```yaml
# Pod has this label:
labels:
  app: bank

# Service selects pods with this label:
selector:
  app: bank
```

```
Service (selector: app=bank)
         ↓  ↓  ↓
   ┌─────┘  │  └─────┐
   ↓        ↓        ↓
Pod(app=bank) Pod(app=bank) Pod(app=bank)
```

---

## 3 Types of Services

```
ClusterIP    → Internal only (inside cluster)
NodePort     → External via Node IP + Port number
LoadBalancer → External via cloud Load Balancer URL
```

---

## Port Terminology

|Port|Where it exists|Purpose|
|---|---|---|
|`targetPort`|Inside the **Pod/Container**|Port the app listens on|
|`port`|On the **Service**|Port clients use to reach the service|
|`nodePort`|On the **Node**|External port (30000-32767)|

```
Client → NodePort (Node) → port (Service) → targetPort (Pod/Container)
         31433              80                 80
```

---

---

# 3. ClusterIP

## What is ClusterIP?

- **Default** service type in Kubernetes
- Exposes the service on an **internal cluster IP only**
- **NOT accessible from outside the cluster**
- Only other pods/services inside the cluster can reach it

---

## ClusterIP Traffic Flow

```
Inside Cluster:
──────────────────────────────────────────────────
Other Pod → ClusterIP Service → Pods (app: bank)
              (stable IP:80)
──────────────────────────────────────────────────

From Internet: ❌ NOT POSSIBLE
```

---

## When to Use ClusterIP?

✅ **Use for:**

- Databases (MySQL, PostgreSQL, MongoDB) — should stay private
- Internal microservices talking to each other
- Backend services not meant for public access
- Redis, RabbitMQ, Kafka

❌ **Do NOT use for:**

- Anything that needs to be accessed from outside the cluster

---

## ClusterIP YAML

```yaml
# service.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ib-deployment
  labels:
    app: bank
spec:
  replicas: 3
  selector:
    matchLabels:
      app: bank
  template:
    metadata:
      labels:
        app: bank
    spec:
      containers:
      - name: cont1
        image: trainerreyaz/ib-image:latest
---                                   # ← Separator: 2 resources in 1 file
apiVersion: v1
kind: Service
metadata:
  name: ib-service
spec:
  type: ClusterIP                     # Default type
  selector:
    app: bank                         # Match pods with this label
  ports:
    - port: 80                        # Service listens on port 80
      targetPort: 80                  # Forwards to pod's port 80
```

---

## ClusterIP Commands

```bash
# Create deployment + service
kubectl create -f service.yml

# List all pods
kubectl get pods

# List all services
kubectl get svc
# Output: ib-service  ClusterIP  10.100.x.x  <none>  80/TCP

# Detailed service info
kubectl describe svc ib-service

# See pods with their IPs
kubectl get pods -o wide

# Delete both deployment + service
kubectl delete -f service.yml
```

---

## ClusterIP Visual

```
┌─────────────────────────────────────────┐
│              K8s Cluster                │
│                                         │
│   Other Pod                             │
│       │                                 │
│       ▼                                 │
│   ib-service (ClusterIP: 10.100.x.x)   │
│       │                                 │
│   ┌───┴────────────┐                   │
│   ▼       ▼        ▼                   │
│  Pod1    Pod2    Pod3                   │
│                                         │
│   ❌ Internet cannot reach this         │
└─────────────────────────────────────────┘
```

---

---

# 4. NodePort

## What is NodePort?

- Exposes the service on **each Node's IP** at a **static port**
- Accessible from **outside the cluster** via `<NodeIP>:<NodePort>`
- Port range: **30000 - 32767**
- If you don't specify a port, K8s picks one randomly in that range

---

## NodePort Traffic Flow

```
Internet
    │
    ▼
<NodeIP>:31433      ← NodePort (external)
    │
    ▼
Service port 80     ← Service internal port
    │
    ▼
Pod targetPort 80   ← Container's listening port
```

---

## NodePort YAML (Random Port)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mb-deployment
  labels:
    app: bank
spec:
  replicas: 3
  selector:
    matchLabels:
      app: bank
  template:
    metadata:
      labels:
        app: bank
    spec:
      containers:
      - name: cont1
        image: trainerreyaz/mb-image:latest
---
apiVersion: v1
kind: Service
metadata:
  name: mb-service
spec:
  type: NodePort                   # ← Changed from ClusterIP
  selector:
    app: bank
  ports:
    - port: 80                     # Service port
      targetPort: 80               # Pod container port
      # nodePort not specified → K8s assigns random port (30000-32767)
```

---

## NodePort YAML (Custom Port)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mb-service
spec:
  type: NodePort
  selector:
    app: bank
  ports:
    - port: 80
      targetPort: 80
      nodePort: 31433              # ← Custom port (must be 30000-32767)
```

---

## NodePort Commands

```bash
# Create/update deployment + service
kubectl apply -f service.yml       # apply = create OR update existing

# List services (see the nodePort assigned)
kubectl get svc
# Output: mb-service  NodePort  10.x.x.x  <none>  80:31433/TCP

# Detailed info
kubectl describe svc mb-service

# Access the app
# http://<NodeIP>:31433
```

> ⚠️ **AWS Security Group:** You must open port 31433 in the Node's Security Group (SG):
> 
> - Custom TCP → Port 31433 → Anywhere (0.0.0.0/0)

---

## create vs apply

|Command|Use When|
|---|---|
|`kubectl create`|Creating a **new** resource for the first time|
|`kubectl apply`|Creating **or updating** an existing resource|

> Best practice: Use `kubectl apply` always — it works for both new and existing resources.

---

## NodePort Drawback ⚠️

```
❌ Problem: You have to give customers IP + Port number

http://13.234.78.82:31433   ← Not user-friendly!

Customers don't want to remember IP addresses and port numbers.
Solution → Use LoadBalancer!
```

---

## NodePort Visual

```
┌─────────────────────────────────────────────┐
│                K8s Cluster                  │
│                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │  Node 1  │  │  Node 2  │  │  Node 3  │  │
│  │ :31433 ✅│  │ :31433 ✅│  │ :31433 ✅│  │
│  │   Pod    │  │   Pod    │  │   Pod    │  │
│  └──────────┘  └──────────┘  └──────────┘  │
└─────────────────────────────────────────────┘
         ▲               ▲               ▲
         │               │               │
    Internet can reach any node on :31433
```


# Kubernetes NodePort Service — Ports Notes

## The 3 Ports

|Port|Scope|Purpose|
|---|---|---|
|`nodePort`|External (Node)|Opens on every Node for outside access (30000–32767)|
|`port`|Internal (Cluster)|Used by other Pods inside the cluster|
|`targetPort`|Container|Where your app actually listens inside the Pod|

---

## Traffic Flow

```
External User
     |
     |  http://<NodeIP>:30007
     ▼
[ nodePort: 30007 ]  ← entry point from outside
     |
     ▼
[   port: 80      ]  ← service's internal cluster port
     |
     ▼
[ targetPort: 8080]  ← your app inside the container
```

---

## Quick Explanation

- **nodePort** — the random port (e.g. 30007) assigned on the Node. This is what external users connect to. Auto-assigned by Kubernetes if you don't specify it.
- **port** — the port other Pods use to talk to this service internally via ClusterIP.
- **targetPort** — must match the port your container app is actually running on.

---

## Example YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - port: 80           # internal cluster port
      targetPort: 8080   # container app port
      nodePort: 30007    # external node port (optional)
```

---

## Access

```bash
# From outside the cluster
curl http://<NodeIP>:30007

# From inside the cluster (other Pods)
curl http://my-service:80
```
---

---

# 5. LoadBalancer

## What is LoadBalancer?

- Exposes the service **externally** using your **cloud provider's load balancer**
- AWS → Creates an **ELB (Elastic Load Balancer)**
- GCP → Creates a **Cloud Load Balancer**
- Azure → Creates an **Azure Load Balancer**
- Gives you a **URL** instead of an IP:Port — customer-friendly! ✅

---

## What LoadBalancer Creates Internally

LoadBalancer builds on top of the other types:

```
LoadBalancer
    │
    ├── Creates a ClusterIP   (internal routing)
    ├── Opens NodePort        (port on every node)
    └── Provisions cloud LB   (public URL for customers)
```

---

## LoadBalancer Traffic Flow

```
Customer
    │
    ▼
http://your-elb-url.aws.com        ← Clean URL (map to Route53/R53)
    │
    ▼
AWS ELB (Load Balancer)
    │
    ├──────────────┬──────────────┐
    ▼              ▼              ▼
  Node 1         Node 2         Node 3
  :31433         :31433         :31433
    │              │              │
    ▼              ▼              ▼
   Pod            Pod            Pod
```

---

## LoadBalancer YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: insurance-deployment
  labels:
    app: bank
spec:
  replicas: 3
  selector:
    matchLabels:
      app: bank
  template:
    metadata:
      labels:
        app: bank
    spec:
      containers:
      - name: cont1
        image: trainerreyaz/insurance-image:latest
---
apiVersion: v1
kind: Service
metadata:
  name: mb-service
spec:
  type: LoadBalancer               # ← Changed to LoadBalancer
  selector:
    app: bank
  ports:
    - port: 80
      targetPort: 80
      nodePort: 31433              # Optional: K8s assigns random if not specified
```

---

## LoadBalancer Commands

```bash
# Create deployment + service
kubectl create -f service.yml

# List services — wait for EXTERNAL-IP to appear
kubectl get svc
# Output: mb-service  LoadBalancer  10.x.x.x  a1234.ap-south-1.elb.amazonaws.com  80:31433

# Get detailed info
kubectl describe svc mb-service

# Delete everything
kubectl delete -f service.yml
```

---

## AWS Console Verification

After creating a LoadBalancer service:

1. Go to **AWS Console → EC2 → Load Balancers**
2. You'll see a new ELB created automatically
3. Go to **Target Groups** — check all targets are **InService**
4. Wait a few minutes for health checks to pass
5. Access via the **DNS name** shown in `kubectl get svc`

---

## Mapping to Route 53 (R53)

```
ELB DNS: a1234.ap-south-1.elb.amazonaws.com
    ↓
Route 53 CNAME record
    ↓
myapp.company.com   ← Clean URL for customers! ✅
```

---

## LoadBalancer Visual

```
┌─────────────────────────────────────────────────────┐
│                     AWS Cloud                       │
│                                                     │
│  Customer → myapp.company.com                       │
│                  │                                  │
│                  ▼                                  │
│          AWS ELB (created automatically)            │
│                  │                                  │
│    ┌─────────────┼─────────────┐                   │
│    ▼             ▼             ▼                   │
│  Node 1        Node 2        Node 3                │
│  :31433        :31433        :31433                │
│    │             │             │                   │
│    ▼             ▼             ▼                   │
│   Pod           Pod           Pod                  │
│  (app: bank)  (app: bank)  (app: bank)             │
└─────────────────────────────────────────────────────┘
```

---

---

# 6. Services Quick Comparison

## Side-by-Side Comparison

|Feature|ClusterIP|NodePort|LoadBalancer|
|---|---|---|---|
|Accessible from|Inside cluster only|Outside via Node IP|Outside via cloud URL|
|Internet access|❌ No|✅ Yes (IP:Port)|✅ Yes (Clean URL)|
|Port range|Any|30000-32767|Any|
|Cloud LB created?|❌ No|❌ No|✅ Yes|
|Customer friendly?|N/A (internal)|❌ No (IP+Port)|✅ Yes (URL)|
|Cost|Free|Free|💰 Cloud LB costs money|
|Use case|Databases, internal services|Dev/testing, direct access|Production, customer-facing apps|

---

## When to Use What?

```
Is it an internal service (DB, cache, backend)?
    → Use ClusterIP ✅

Do you need external access for testing/dev?
    → Use NodePort ✅

Do you need production-grade external access with a URL?
    → Use LoadBalancer ✅
```

---

## Port Summary

```
LoadBalancer traffic path:

Internet
    │
    ▼
ELB :80 (or 443)          ← Cloud Load Balancer
    │
    ▼
Node :31433                ← nodePort (30000-32767)
    │
    ▼
Service :80                ← port
    │
    ▼
Container :80              ← targetPort
```

---

## All Service Types in One Picture

```
┌─────────────────────────────────────────────────────────────┐
│                        K8s Cluster                          │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  ClusterIP Service                                    │  │
│  │  Only reachable from INSIDE the cluster               │  │
│  │  Other Pod → ClusterIP → Pods                         │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  NodePort Service                                     │  │
│  │  Reachable from OUTSIDE via Node IP + Port            │  │
│  │  http://13.234.78.82:31433                            │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  LoadBalancer Service                                 │  │
│  │  Reachable from OUTSIDE via clean URL                 │  │
│  │  http://myapp.company.com                             │  │
│  │  (Cloud LB auto-created)                              │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
        ▲                   ▲                   ▲
      Internal          Dev/Testing          Production
```

---

## Key Commands Cheat Sheet

```bash
# Services
kubectl get svc                          # List all services
kubectl describe svc <name>              # Detailed service info
kubectl delete svc <name>                # Delete a service

# Create vs Apply
kubectl create -f service.yml            # Create new resource
kubectl apply -f service.yml             # Create OR update existing

# Static Pods
kubectl get pods -n kube-system -o wide  # See control plane static pods

# Events (debugging)
kubectl get events                       # See all cluster events

# Cluster info
kops get cluster                         # Get cluster details
kubectl get nodes                        # List all nodes
```

---

## Key Takeaways

```
Static Pod   → Managed by Kubelet directly, YAML in /etc/kubernetes/manifests/
               Mirror pod visible in kubectl but cannot be deleted via kubectl

ClusterIP    → Internal only, perfect for databases and private services

NodePort     → Exposes on Node IP:Port (30000-32767), accessible from internet
               but not customer-friendly (raw IP + port)

LoadBalancer → Best for production, creates cloud LB with clean URL
               Built on top of ClusterIP + NodePort
               Map ELB URL to Route53 for custom domain
```

---

_Notes compiled from Kubernetes Static Pods and Services practicals_