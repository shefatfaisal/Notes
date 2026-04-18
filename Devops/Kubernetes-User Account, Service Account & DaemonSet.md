

### Complete Study Notes

---

## Table of Contents

1. [User Account (Human RBAC)](https://claude.ai/new?incognito#1-user-account-human-rbac)
2. [Service Account (Application RBAC)](https://claude.ai/new?incognito#2-service-account-application-rbac)
3. [DaemonSet](https://claude.ai/new?incognito#3-daemonset)
4. [Quick Comparison Tables](https://claude.ai/new?incognito#4-quick-comparison-tables)

---

# 1. User Account (Human RBAC)

## What is a User Account?

A **User Account** represents a **human** (developer, admin, DevOps engineer) who needs access to the Kubernetes cluster.

> ⚠️ **Key Fact:** Kubernetes does NOT store users internally. There is no `kubectl create user` command. Users exist **outside** the cluster and authenticate using **certificates, OIDC, or other external providers.**

---

## How Kubernetes Identifies a User

Kubernetes trusts identities that come from **TLS certificates** signed by the cluster's own Certificate Authority (CA).

| Certificate Field  | Kubernetes Meaning       |
| ------------------ | ------------------------ |
| `CN` (Common Name) | Becomes the **username** |
| `O` (Organization) | Becomes the **group**    |

So when you create a cert with `/CN=dev-user/O=dev-group`, Kubernetes sees:

- **Username:** `dev-user`
- **Group:** `dev-group`

---

## Authentication Methods

|Method|Use Case|
|---|---|
|Client Certificates|Local clusters, labs, learning|
|OIDC (Azure AD, Google, Okta)|Production environments|
|Static token files|Simple/legacy setups|
|Webhook token auth|Custom auth integrations|

---

## Full Step-by-Step: Create a Human User

### 🎯 Goal

Create `dev-user` who can **only list and get pods** in the `development` namespace.

---

### Step 1 — Generate Private Key & CSR

```bash
# Create a 2048-bit private key
openssl genrsa -out dev-user.key 2048

# Create a Certificate Signing Request (CSR)
openssl req -new -key dev-user.key -out dev-user.csr -subj "/CN=dev-user/O=dev-group"
```

**Explanation:**

- `genrsa -out dev-user.key 2048` → Creates the **private key** (keep this secret!)
- `req -new` → Creates a **CSR** (a request asking K8s to trust this identity)
- `/CN=dev-user` → Sets the username inside the certificate
- `/O=dev-group` → Sets the group inside the certificate

> 💡 Think of the CSR like a job application — you're asking the Kubernetes CA (HR department) to issue you an official ID card.

---

### Step 2 — Base64 Encode the CSR

```bash
cat dev-user.csr | base64 | tr -d "\n"
```

This outputs a long base64 string. **Copy it** — you need it in the next step.

> Why? Kubernetes only accepts CSR data in base64 format inside YAML manifests.

---

### Step 3 — Submit CSR to Kubernetes

```yaml
# csr.yml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: dev-user
spec:
  request: <paste_base64_output_here>
  signerName: kubernetes.io/kube-apiserver-client
  usages:
    - client auth
```

```bash
kubectl create -f csr.yml
```

**Explanation:**

- `kind: CertificateSigningRequest` → A real K8s object that holds your request
- `signerName: kubernetes.io/kube-apiserver-client` → Tells K8s this is for **user authentication**
- `usages: client auth` → This cert is for a human logging in, not a server

---

### Step 4 — Approve the CSR (Admin Action)

```bash
# Approve the request
kubectl certificate approve dev-user

# Extract the signed certificate
kubectl get csr dev-user -o jsonpath='{.status.certificate}' | base64 --decode > dev-user.crt
```

After this you have:

- `dev-user.key` → Private key
- `dev-user.crt` → Signed certificate (K8s now trusts this user)

---

### Step 5 — Add User to Kubeconfig

```bash
# Register credentials
kubectl config set-credentials dev-user \
  --client-certificate=dev-user.crt \
  --client-key=dev-user.key

# Create a context (cluster + namespace + user bundle)
kubectl config set-context dev-user-context \
  --cluster=reyaz.k8s.local \
  --namespace=development \
  --user=dev-user

# To find your cluster name:
kubectl config get-clusters
```

**What is a Context?**

A **context** bundles three things together:

```
Context = Cluster + Namespace + User
```

When you switch context, kubectl knows exactly which cluster to talk to, which namespace to use, and who you are.

---

### Step 6 — Create Role & RoleBinding

```yaml
# role.yml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: development
  name: pod-reader
rules:
- apiGroups: [""]        # "" = core API group (pods, services, etc.)
  resources: ["pods"]
  verbs: ["get", "list"] # READ ONLY
```

```yaml
# binding.yml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-user-binding
  namespace: development
subjects:
- kind: User
  name: dev-user           # Must EXACTLY match CN= from certificate
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

```bash
kubectl apply -f role.yml
kubectl apply -f binding.yml
```

> ⚠️ **Critical:** The `name: dev-user` in RoleBinding **must exactly match** the `CN=dev-user` from the certificate. This is how K8s links the cert identity to RBAC permissions.

---

### Step 7 — Test the Access

```bash
# List pods as dev-user ✅ (should work)
kubectl --context=dev-user-context get pods

# Try to delete a pod ❌ (should be denied)
kubectl --context=dev-user-context delete pod <pod-name>
```

---

### Step 8 — Cleanup (Delete Everything)

```bash
# Delete RBAC objects
kubectl delete rolebinding dev-user-binding -n development
kubectl delete role pod-reader -n development

# Delete the CSR from cluster
kubectl delete csr dev-user

# Remove from kubeconfig
kubectl config delete-context dev-user-context
kubectl config delete-user dev-user

# Delete local cert files
rm -f dev-user.key dev-user.crt dev-user.csr
```

**Cleanup order matters — delete in reverse creation order:**

```
RoleBinding → Role → CSR → Context → User → Local files
```

---

### Full User Account Flow

```
1. openssl genrsa         → Create private key
        ↓
2. openssl req -new       → Create CSR (certificate request)
        ↓
3. base64 encode CSR      → Prepare for K8s
        ↓
4. kubectl create csr.yml → Submit to Kubernetes
        ↓
5. kubectl approve        → Admin approves → get signed .crt
        ↓
6. kubectl config         → Add user + context to kubeconfig
        ↓
7. Create Role            → Define what's allowed
        ↓
8. Create RoleBinding     → Link user to role
        ↓
9. Test with --context    → Verify access works
```

---

---

# 2. Service Account (Application RBAC)

## What is a Service Account?

A **Service Account** represents an **application, bot, or automated process** that needs to interact with the Kubernetes API.

> Unlike User Accounts, Service Accounts **ARE stored inside Kubernetes** as real objects and can be created with `kubectl`.

---

## User Account vs Service Account

|Feature|User Account|Service Account|
|---|---|---|
|Used by|Humans|Apps / Bots / CI-CD pipelines|
|Stored in Kubernetes?|❌ No|✅ Yes|
|Created with kubectl?|❌ No|✅ Yes|
|Managed by K8s?|❌ No|✅ Yes|
|Scope|Cluster-wide|Namespace-specific|
|Authentication|Certs, OIDC, Azure AD|Auto-generated token|
|Example|Dev logging into cluster|Pod accessing the K8s API|

### Simple Analogy 🏢

Think of a company office building:

- **User Account** = Employee badge issued by HR (external system) for a human
- **Service Account** = Access card issued internally for a robot/machine/automated system

---

## Full Step-by-Step: Create a Service Account

### 🎯 Goal

Give service account `jack` permission to **get, list, and watch pods** in the `dev` namespace.

---

### Step 1 — Create the Namespace

```bash
kubectl create ns dev

# Set it as default namespace for your session
kubectl config set-context --current --namespace=dev
```

---

### Step 2 — Create the Service Account

```yaml
# serviceaccount.yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jack
  namespace: dev
```

```bash
kubectl create -f serviceaccount.yml

# Or create directly without YAML:
kubectl create serviceaccount jack -n dev
```

---

### Step 3 — Create a Role (Define Permissions)

```yaml
# role.yml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: dev-pod-reader
rules:
- apiGroups: [""]           # "" = core API group (pods live here)
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

```bash
kubectl create -f role.yml
```

**Available Verbs Reference:**

|Verb|Action|
|---|---|
|`get`|Read a specific resource|
|`list`|List all resources of a type|
|`watch`|Stream live updates|
|`create`|Create new resources|
|`update`|Modify existing resources|
|`patch`|Partially modify resources|
|`delete`|Remove resources|

**Available Resources Reference:**

|Resource|API Group|
|---|---|
|pods, services, configmaps, secrets|`""` (core)|
|deployments, replicasets, daemonsets|`apps`|
|roles, rolebindings|`rbac.authorization.k8s.io`|

---

### Step 4 — Create a RoleBinding (Link SA to Role)

```yaml
# rolebinding.yml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: dev
subjects:
- kind: User          # Can also be: ServiceAccount or Group
  name: jack
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: dev-pod-reader
  apiGroup: rbac.authorization.k8s.io
```

```bash
kubectl create -f rolebinding.yml
```

---

### Step 5 — Verify Access

```bash
# Can jack list pods? ✅
kubectl auth can-i list pods --namespace=dev --as=jack

# Can jack create pods? ❌
kubectl auth can-i create pods --namespace=dev --as=jack

# Can jack delete pods? ❌
kubectl auth can-i delete pods --namespace=dev --as=jack
```

---

### Useful Commands

```bash
# List all roles in a namespace
kubectl get roles -n dev

# See details of a role
kubectl describe role dev-pod-reader -n dev

# See details of a rolebinding
kubectl describe rolebinding read-pods -n dev

# List all service accounts
kubectl get serviceaccounts -n dev

# List all rolebindings
kubectl get rolebindings -n dev
```

---

### Service Account Flow

```
Create ServiceAccount (jack)
        ↓
Create Role (dev-pod-reader)   ← Define what's allowed
        ↓
Create RoleBinding (read-pods) ← Link jack → role
        ↓
Verify with: kubectl auth can-i
```

---

## Role vs ClusterRole (Namespace vs Cluster-wide)

|Component|Scope|Use When|
|---|---|---|
|`Role`|Single namespace|Restrict access to one namespace|
|`ClusterRole`|Entire cluster|Need access across all namespaces|
|`RoleBinding`|Single namespace|Bind a Role within a namespace|
|`ClusterRoleBinding`|Entire cluster|Bind a ClusterRole cluster-wide|

```yaml
# ClusterRole example (cluster-wide read access)
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

```yaml
# ClusterRoleBinding example
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-read-pods
subjects:
- kind: ServiceAccount
  name: jack
  namespace: dev
roleRef:
  kind: ClusterRole
  name: cluster-pod-reader
  apiGroup: rbac.authorization.k8s.io
```

---

---

# 3. DaemonSet

## What is a DaemonSet?

A **DaemonSet** ensures that **exactly one pod runs on every node** in the cluster.

- When a **new node joins** → Pod is automatically scheduled on it
- When a **node leaves** → Its pod is automatically removed
- No manual intervention needed — it's fully automatic

---

## When to Use DaemonSet?

Use DaemonSets for **system-level services** that every node needs:

|Use Case|Example Tools|
|---|---|
|📊 Monitoring agents|Prometheus Node Exporter, Datadog|
|📝 Log collectors|Fluentd, Filebeat, Logstash|
|🌐 Networking / CNI plugins|Calico, Weave, Flannel|
|🔒 Security scanners|Falco, Twistlock|
|💾 Storage agents|Ceph, GlusterFS|

---

## DaemonSet vs Deployment

|Feature|Deployment|DaemonSet|
|---|---|---|
|You set replica count|✅ Yes|❌ No|
|Runs on every node|❌ No|✅ Always|
|Scales with nodes automatically|❌ No|✅ Yes|
|Good for stateless apps|✅ Yes|Possible but not typical|
|Good for system agents|❌ No|✅ Yes|
|Can scale to 0|✅ Yes|❌ No|

---

## DaemonSet YAML

```yaml
# daemon.yml
apiVersion: apps/v1
kind: DaemonSet           # Changed from Deployment
metadata:
  name: mb-daemon
  labels:
    app: bank
spec:
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
        # No replicas field — node count determines pod count!
```

**Key differences from Deployment YAML:**

1. `kind: DaemonSet` instead of `kind: Deployment`
2. **No `replicas` field** — K8s decides based on node count

---

## DaemonSet Commands

```bash
# Create DaemonSet
kubectl create -f daemon.yml

# List pods (will show 1 pod per node)
kubectl get pods

# View all daemonsets in default namespace
kubectl get daemonset
# OR shorthand:
kubectl get ds

# View daemonsets in a specific namespace
kubectl get daemonset --namespace=default

# Describe a daemonset (see detailed info)
kubectl describe daemonset mb-daemon

# Delete a specific daemonset
kubectl delete daemonset mb-daemon --namespace=default

# Delete ALL daemonsets in a namespace
kubectl delete daemonset --all --namespace=default
```

---

## How DaemonSet Scheduling Works

```
Cluster with 3 nodes:
┌─────────┐    ┌─────────┐    ┌─────────┐
│ Node 1  │    │ Node 2  │    │ Node 3  │
│         │    │         │    │         │
│  Pod ✅ │    │  Pod ✅ │    │  Pod ✅ │
└─────────┘    └─────────┘    └─────────┘

New node joins:
┌─────────┐
│ Node 4  │
│         │ ← DaemonSet auto-schedules pod here
│  Pod ✅ │
└─────────┘

Node removed:
             ┌─────────┐
             │ Node 2  │ ← Removed
             │         │
             │  Pod 🗑️ │ ← Auto-cleaned up
             └─────────┘
```

---

## DaemonSet with Node Selector (Advanced)

You can run DaemonSet pods on **only specific nodes** using `nodeSelector`:

```yaml
spec:
  template:
    spec:
      nodeSelector:
        role: monitoring    # Only runs on nodes labeled role=monitoring
      containers:
      - name: monitor-agent
        image: prometheus/node-exporter
```

Label a node:

```bash
kubectl label node <node-name> role=monitoring
```

---

## Real-World Example: Log Collector DaemonSet

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-logger
  namespace: kube-system
spec:
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      containers:
      - name: fluentd
        image: fluent/fluentd:v1.14
        volumeMounts:
        - name: varlog
          mountPath: /var/log      # Mount node's log directory
      volumes:
      - name: varlog
        hostPath:
          path: /var/log           # Access logs from the actual node
```

This puts Fluentd on every node and gives it access to the node's log files — a classic DaemonSet pattern.

---

---

# 4. Quick Comparison Tables

## RBAC Components Summary

|Component|Scope|What it does|
|---|---|---|
|`Role`|Namespace|Defines allowed actions on resources|
|`ClusterRole`|Cluster-wide|Defines allowed actions across all namespaces|
|`RoleBinding`|Namespace|Links a Role to a User/SA|
|`ClusterRoleBinding`|Cluster-wide|Links a ClusterRole to a User/SA|
|`ServiceAccount`|Namespace|Identity for apps/pods|

---

## Authentication Summary

|Who|Identity Type|Auth Method|
|---|---|---|
|Human (lab)|User Account|Client Certificate|
|Human (prod)|User Account|OIDC (Azure AD, Google)|
|Application|Service Account|Auto-generated token|
|CI/CD pipeline|Service Account|Token mounted in pod|

---

## Workload Types Summary

|Workload|Replicas|Best For|
|---|---|---|
|`Deployment`|You control|Stateless web apps, APIs|
|`StatefulSet`|You control|Databases, ordered apps|
|`DaemonSet`|1 per node (auto)|System agents on every node|
|`Job`|Runs to completion|Batch tasks|
|`CronJob`|Scheduled|Periodic tasks|

---

## Key Takeaways

```
User Account  → Human identity → Cert/OIDC → Exists OUTSIDE K8s
ServiceAccount → App identity  → Token     → Exists INSIDE K8s (K8s object)

Both go through RBAC:
  Role/ClusterRole     → defines WHAT is allowed
  RoleBinding/ClusterRoleBinding → defines WHO gets it

DaemonSet → 1 pod per node, auto-manages itself, used for system services
```

---

_Notes compiled from Kubernetes RBAC and DaemonSet practicals_