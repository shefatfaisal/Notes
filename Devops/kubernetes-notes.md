# ☸️ Kubernetes (K8s) Complete Guide

> A structured, hands-on reference for Minikube, Pods, ReplicaSets, Deployments, and Linux essentials.

---

## 📋 Table of Contents

- [⚙️ Minikube Setup](https://claude.ai/new?incognito#%EF%B8%8F-minikube-setup)
- [🖥️ kubectl — The CLI](https://claude.ai/new?incognito#%EF%B8%8F-kubectl--the-cli)
- [📦 Pods](https://claude.ai/new?incognito#-pods)
    - [Imperative (Commands)](https://claude.ai/new?incognito#1-imperative-commands)
    - [Declarative (Manifest Files)](https://claude.ai/new?incognito#2-declarative-manifest-files)
    - [Manifest File Anatomy](https://claude.ai/new?incognito#-manifest-file-anatomy)
    - [Multi-Container Pods](https://claude.ai/new?incognito#-multi-container-pods)
- [🎨 KubeColor](https://claude.ai/new?incognito#-kubecolor)
- [♻️ ReplicaSet](https://claude.ai/new?incognito#%EF%B8%8F-replicaset)
    - [Labels & Selectors](https://claude.ai/new?incognito#labels--selectors)
    - [Scaling](https://claude.ai/new?incognito#scaling-scale-out--scale-in)
    - [ReplicaSet vs ReplicationController](https://claude.ai/new?incognito#replicaset-vs-replicationcontroller)
    - [Cons of ReplicaSet](https://claude.ai/new?incognito#cons-of-replicaset)
- [🚨 Troubleshooting](https://claude.ai/new?incognito#-troubleshooting)
    - [CrashLoopBackOff](https://claude.ai/new?incognito#crashloopbackoff)
    - [Common Image Mistakes](https://claude.ai/new?incognito#common-image-mistakes)
- [⚠️ Drawbacks & Next Steps](https://claude.ai/new?incognito#%EF%B8%8F-drawbacks--next-steps)
- [🚀 Deployments](https://claude.ai/new?incognito#-deployments)
    - [Rolling Updates](https://claude.ai/new?incognito#rolling-updates)
    - [Rollback](https://claude.ai/new?incognito#rollback)
    - [Deployment Strategies](https://claude.ai/new?incognito#deployment-strategies)
    - [Exposing Ports](https://claude.ai/new?incognito#exposing-ports)
- [⚙️ Kubernetes Jobs](https://claude.ai/new?incognito#%EF%B8%8F-kubernetes-jobs)
    - [Non-Parallel Jobs](https://claude.ai/new?incognito#1-non-parallel-jobs)
    - [Parallel Fixed Count](https://claude.ai/new?incognito#2-parallel-jobs-with-fixed-completion-count)
    - [Parallel Work Queue](https://claude.ai/new?incognito#3-parallel-jobs-with-work-queue)
- [🕐 CronJobs](https://claude.ai/new?incognito#-cronjobs)
- [🐧 Linux Essentials for K8s](https://claude.ai/new?incognito#-linux-essentials-for-k8s)
    - [.bashrc](https://claude.ai/new?incognito#bashrc)
    - [PATH variable](https://claude.ai/new?incognito#export-path-explained)
    - [Vim Indentation](https://claude.ai/new?incognito#-vim-indentation)

---

## ⚙️ Minikube Setup

### Requirements

|Item|Specification|
|---|---|
|OS|Ubuntu 24|
|Instance|t2.small|
|Storage|Minimum **8 GB** volume|

### Installation Script

```bash
#!/bin/bash
# minikube.sh — Full setup script

sudo apt update -y
sudo apt upgrade -y
sudo apt install curl wget apt-transport-https -y

# Install Docker
sudo curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Install Minikube
sudo curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo mv minikube-linux-amd64 /usr/local/bin/minikube
sudo chmod +x /usr/local/bin/minikube
sudo minikube version

# Install kubectl
sudo curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
sudo echo "$(cat kubectl.sha256) kubectl" | sha256sum --check
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Start Minikube
sudo minikube start --driver=docker --force
```

---

## 🖥️ kubectl — The CLI

> `kubectl` is the command-line interface used to interact with a Kubernetes cluster. You can create, manage, and monitor **pods**, **services**, **deployments**, and other resources.

📁 Configuration lives at: `$HOME/.kube/`

### Essential Commands

```bash
minikube status       # Check minikube health
kubectl get nodes     # List cluster nodes
kubectl get pods      # List all pods
```

---

## 📦 Pods

### What is a Pod?

|Property|Description|
|---|---|
|🔹 Smallest unit|The smallest deployable unit in Kubernetes|
|🔹 Group of containers|One or more containers bundled together|
|🔹 Ephemeral|Short-lived objects — not persistent by design|
|🔹 Shared network|Containers in a pod share the **same network namespace**|
|🔹 Shared storage|Containers can share the **same storage volumes**|
|🔹 K8s communication|Kubernetes communicates with **pods**, not containers directly|

> 💡 **Best Practice:** By default, use **one container per pod**. Only group containers that are tightly coupled.

---

### 1. Imperative (Commands)

> ⚡ Quick way to create pods — good for testing, **not recommended for production**.

```bash
# Create a pod named 'pod1' using the nginx image
kubectl run pod1 --image nginx

# List pods (all equivalent)
kubectl get pods
kubectl get pod
kubectl get po

# Get detailed pod info with node placement
kubectl get pod -o wide

# Describe a pod (events, image, IPs, etc.)
kubectl describe pod pod1

# View pod logs
kubectl logs pod1

# Delete a pod
kubectl delete pod pod1
```

#### 🔑 The `--` Separator Explained

```bash
kubectl exec -it my-pod -- /bin/bash
```

**Why do we use `--` here?**

The double dash `--` is a **POSIX standard argument separator**. It tells the shell and `kubectl` that _everything after this point_ is **not** a `kubectl` flag — it belongs to the command being run **inside** the container.

```
kubectl exec -it my-pod  --  /bin/bash -c "ls -la"
│─────── kubectl part ───│  │─── container command part ───│
```

**Without `--` — Ambiguity Problem:**

```bash
# ❌ Ambiguous — does '-c' belong to kubectl or to bash?
kubectl exec -it my-pod /bin/bash -c "echo hello"

# ✅ Clear — kubectl sees only '-it my-pod', bash gets '-c "echo hello"'
kubectl exec -it my-pod -- /bin/bash -c "echo hello"
```

**Real-world examples:**

```bash
# Open an interactive bash shell inside the pod
kubectl exec -it my-pod -- /bin/bash

# Run a one-off command inside the pod
kubectl exec -it my-pod -- ls /usr/share/nginx/html

# Pass flags to the inner command safely
kubectl exec -it my-pod -- /bin/bash -c "cat /etc/hosts"

# Specify a container in a multi-container pod
kubectl exec -it my-pod -c my-container -- /bin/sh
```

> 🧠 **Memory tip:** Think of `--` as a "wall" between kubectl's world and the container's world. Flags on the left are for kubectl; everything on the right goes straight into the container.

---

#### Example Session

```bash
kubectl run reyaz --image nginx
kubectl get pods
kubectl logs reyaz
kubectl exec -it reyaz -- /bin/bash
  # Inside the container:
  cd /usr/share/nginx/html
  cat index.html
  exit
kubectl delete pod reyaz
```

---

### 2. Declarative (Manifest Files)

> 📄 The **recommended** production approach. Manifest files are reusable, version-controllable, and consistent.

---

### 🧩 Manifest File Anatomy

Every Kubernetes manifest requires these **four mandatory fields**:

```yaml
apiVersion:   # Which API library to use
kind:         # What object to create
metadata:     # Name, labels, annotations
spec:         # The actual configuration
```

#### `apiVersion` Reference Table

|Object|apiVersion|
|---|---|
|Pod|`v1`|
|Service|`v1`|
|Namespace|`v1`|
|Secret|`v1`|
|ReplicaSet|`apps/v1`|
|Deployment|`apps/v1`|
|Job|`batch/v1`|

```bash
# Discover all available API versions
kubectl api-versions

# Discover all API resources with their kind/shortname
kubectl api-resources
```

#### `kind` — Common Object Types

```
Pod | Deployment | Service | Ingress | Job | ReplicaSet | Namespace
```

#### `metadata` — Object Identity

```yaml
metadata:
  name: my-pod           # Unique name in the namespace
  labels:
    app: bank            # Key-value pairs for grouping & selecting
    env: production
```

> ⚠️ **Common mistake:** `label:` is WRONG — must always be `labels:` (plural). Kubernetes silently ignores `label:` and your pods get no labels, breaking selector matching. Fix: `:%s/label:/labels/` in vim.

#### `spec` — Container Configuration

Contains: image name, environment variables, port mappings, replica count, resource limits.

---

### 📝 Pod Manifest Example

```yaml
# pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: pod1
spec:
  containers:
    - image: nginx
      name: cont1
```

```bash
kubectl create -f pod.yml      # Create from file
kubectl describe pod pod1      # Inspect the pod
kubectl delete pod pod1        # Delete the pod
```

> ⚠️ **Drawback:** Once a pod is deleted, it's **gone forever**. No self-healing. Use **ReplicaSet** for high availability.

---

### 🐳 Multi-Container Pods

Add multiple entries under `containers:` — each `-` item is a separate container.

```yaml
# multi-container-pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
  labels:
    app: myapp
spec:
  containers:

    - name: nginx-container        # Container 1 — web server
      image: nginx
      ports:
        - containerPort: 80

    - name: redis-container        # Container 2 — cache
      image: redis
      ports:
        - containerPort: 6379

    - name: busybox-container      # Container 3 — sidecar/helper
      image: busybox
      command: ["sleep", "3600"]   # keeps it running
```

#### Exec into a specific container

```bash
# Must use -c to specify which container
kubectl exec -it multi-pod -c nginx-container -- /bin/bash
kubectl exec -it multi-pod -c redis-container -- /bin/bash
kubectl exec -it multi-pod -c busybox-container -- /bin/sh

# Without -c → connects to the FIRST container by default (with warning)
```

#### What containers in the same pod share

|Resource|Shared?|Details|
|---|---|---|
|Network|✅ Yes|All containers use `localhost` to talk to each other|
|Storage (volumes)|✅ Yes|Mount same `volume` to share files|
|CPU / Memory|❌ No|Each container has its own resource limits|
|Filesystem|❌ No|Separate unless a shared volume is mounted|

#### Shared Volume Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: shared-volume-pod
spec:
  volumes:
    - name: shared-data
      emptyDir: {}               # temp storage, dies with pod

  containers:
    - name: writer
      image: busybox
      command: ["sh", "-c", "echo Hello > /data/file.txt && sleep 3600"]
      volumeMounts:
        - name: shared-data
          mountPath: /data

    - name: reader
      image: busybox
      command: ["sh", "-c", "cat /data/file.txt && sleep 3600"]
      volumeMounts:
        - name: shared-data
          mountPath: /data       # same volume — reader sees writer's files instantly
```

---

#### Why `-` sometimes and not other times in YAML?

|Symbol|Means|Use when|
|---|---|---|
|`-`|List item (array)|multiple things of same type|
|no `-`|Single value (map)|just one key and its value|

**Ask yourself — "can there be more than one of this?"**

```yaml
containers:    → YES, multiple containers      → uses -
ports:         → YES, multiple ports           → uses -
env:           → YES, multiple env vars        → uses -
restartPolicy: → NO, only one restart policy   → no -
image:         → NO, only one image            → no -
replicas:      → NO, only one number           → no -
```

---

#### Full `template` spec — all available fields

```yaml
template:
  metadata:
    labels:
      app: myapp
    annotations:
      description: "my app"

  spec:
    containers:
      - name: my-container
        image: nginx:1.21
        ports:
          - containerPort: 80
        env:
          - name: ENV_MODE
            value: "production"
          - name: DB_PASSWORD
            valueFrom:
              secretKeyRef:
                name: my-secret
                key: password
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
        volumeMounts:
          - name: my-volume
            mountPath: /data
        livenessProbe:             # restart if this fails
          httpGet:
            path: /healthz
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 10
        readinessProbe:            # send traffic only if this passes
          httpGet:
            path: /ready
            port: 80
          initialDelaySeconds: 3
          periodSeconds: 5
        command: ["nginx"]
        args: ["-g", "daemon off;"]

    initContainers:
      - name: init-setup
        image: busybox
        command: ["sh", "-c", "echo initializing... && sleep 2"]

    volumes:
      - name: my-volume
        emptyDir: {}
      - name: config-volume
        configMap:
          name: my-configmap

    nodeSelector:
      disktype: ssd

    restartPolicy: Always         # Always | OnFailure | Never
    serviceAccountName: my-service-account
```

|Section|What it does|
|---|---|
|`labels`|Group & select pods|
|`annotations`|Add notes/metadata (not used for selection)|
|`containers`|Main app containers|
|`initContainers`|Run setup tasks **before** main containers start|
|`env`|Pass environment variables|
|`resources`|Set CPU/memory requests and limits|
|`volumeMounts`|Mount storage into a container|
|`volumes`|Define storage sources|
|`livenessProbe`|Restart container if unhealthy|
|`readinessProbe`|Only send traffic when container is ready|
|`nodeSelector`|Pin pods to specific nodes|
|`restartPolicy`|What to do when a container crashes|

---

## 🎨 KubeColor

A drop-in colorized replacement for `kubectl` output.

```bash
wget https://github.com/hidetatz/kubecolor/releases/download/v0.0.25/kubecolor_0.0.25_Linux_x86_64.tar.gz
tar -zxvf kubecolor_0.0.25_Linux_x86_64.tar.gz
chmod +x kubecolor
mv kubecolor /usr/local/bin/

# Use it just like kubectl
kubecolor get po
```

---

## ♻️ ReplicaSet

### What is a ReplicaSet?

A ReplicaSet ensures a **specified number of pod replicas** are running at all times.

```
You create RS  →  RS creates Pods  →  RS watches & heals Pods
```

---

### Labels & Selectors

|Concept|Purpose|
|---|---|
|**Label**|A key-value tag applied to pods (e.g., `app: bank`)|
|**Selector**|How the ReplicaSet finds and owns its pods|
|**matchLabels**|Tells the RS: _"manage all pods with this label"_|

> 💡 The key name `app` is just a **community convention** — not a Kubernetes reserved word. You can use `tier`, `env`, `module` — anything. The only rule: `selector.matchLabels` and `template.labels` must have the **exact same key-value pair**.

```yaml
# All valid ✅ — as long as selector and template match
labels:
  app: myapp        # most common convention
labels:
  tier: frontend
labels:
  module: banking
```

---

### ReplicaSet Manifest

```yaml
# replicaset.yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: ib-rs
  labels:
    app: bank
spec:
  replicas: 3
  selector:
    matchLabels:
      app: bank            # finds pods with this label
  template:                # pod blueprint — no 'kind' needed here
    metadata:
      labels:
        app: bank
    spec:
      containers:
      - name: cont1
        image: trainerreyaz/ib-image:latest
```

> ❓ **Why no `kind` inside `template`?** Kubernetes hardcodes `template` as always being a Pod blueprint. The object hierarchy is fixed: `Deployment → ReplicaSet → Pod → Container`. You cannot nest other kinds inside a template — they must be separate top-level manifests.

### ReplicaSet Commands

```bash
kubectl create -f replicaset.yml     # Create
kubectl get rs                        # List
kubectl get rs -o wide                # List with details
kubectl describe rs ib-rs             # Inspect
kubectl get pods --show-labels        # List pods with labels
kubectl get pods -l app=bank          # Filter by label
kubectl get pods --watch              # Watch live (separate terminal)
kubectl delete pods -l app=bank       # Delete by label
kubectl delete rs ib-rs               # Delete the ReplicaSet
```

---

### Scaling (Scale Out & Scale In)

```bash
kubectl scale rs/ib-rs --replicas=10  # Scale OUT
kubectl scale rs/ib-rs --replicas=5   # Scale IN
```

> 🔄 **LIFO:** Last In, First Out — most recently created pods are deleted first during scale-in.

---

### ReplicaSet vs ReplicationController

|Feature|ReplicationController|ReplicaSet|
|---|---|---|
|Selector type|Equality-based only|**Set-based** (more powerful)|
|`matchLabels` support|❌ No|✅ Yes|
|`rolling-update` command|✅ Works|❌ Doesn't work|
|Status|⚠️ Deprecated|✅ Current standard|
|Recommended?|❌ No|✅ Use with Deployments|

---

### Cons of ReplicaSet

#### ❌ Cannot do Rolling Updates

```bash
kubectl edit rs/my-rs             # change image
kubectl describe pod -l app=myapp | grep Image
# Still OLD image — existing pods not updated
# Only scale-out pods get new image → MIXED CLUSTER ❌
```

#### ❌ Cannot Rollback

```bash
kubectl rollout undo rs/my-rs     # ❌ does NOT work
kubectl rollout history rs/my-rs  # ❌ does NOT work
```

#### ❌ Causes Downtime on Update

```bash
kubectl delete rs my-rs           # all pods die ❌
kubectl create -f replicaset.yml  # new pods start → GAP = DOWNTIME
```

#### ❌ No Deployment Strategies

|Strategy|Available in RS?|
|---|---|
|Rolling Update|❌|
|Blue/Green|❌|
|Canary|❌|

#### Summary

|Feature|ReplicaSet|Deployment|
|---|---|---|
|Self healing|✅|✅|
|Scaling|✅ manual|✅ manual + auto|
|Rolling update|❌|✅|
|Rollback|❌|✅|
|Zero downtime|❌|✅|
|Version history|❌|✅|

---

## 🚨 Troubleshooting

### CrashLoopBackOff

```
NAME                    READY   STATUS
fun-588c88656-8bknh     1/1     Running           ✅
fun-89ccff67d-l8dgl     0/1     CrashLoopBackOff  ❌  ← RESTARTS: 4
```

**What it means:**

```
Container starts → crashes → K8s restarts → crashes again
→ waits longer → restarts → crashes → CrashLoopBackOff
```

> Different pod name prefix (e.g. `fun-89ccff67d` vs `fun-588c88656`) means a **Deployment update happened** — new ReplicaSet pods are crashing while old ones run fine.

**How to debug:**

```bash
# Step 1 — check logs
kubectl logs <pod-name>

# Step 2 — check previous (crashed) container logs
kubectl logs <pod-name> --previous

# Step 3 — describe and read Events section at the bottom
kubectl describe pod <pod-name>

# Step 4 — check which image is running
kubectl describe pod <pod-name> | grep Image
```

**Common causes:**

|Reason|How to confirm|
|---|---|
|Wrong image name/tag|`describe pod` → Image field|
|App crashes on startup|`logs --previous` → error message|
|Missing env variable|`logs` → config error|
|Base OS image with no process|exits immediately — see below|
|OOMKilled (RAM limit too low)|`describe` → Events: OOMKilled|

---

### Common Image Mistakes

> ⚠️ **Real example:** Changing `nginx` → `ubuntu` causes CrashLoopBackOff

```
nginx image   → starts web server → runs forever ✅
ubuntu image  → starts → nothing to do → exits → crash ❌
```

**Why ubuntu crashes:** It's a base OS image with no default foreground process. Without a terminal, `/bin/bash` exits immediately → Kubernetes restarts it → infinite loop.

**Fix:**

```yaml
# Option 1 — give it a process
containers:
  - name: cont1
    image: ubuntu
    command: ["sleep", "infinity"]

# Option 2 — go back to nginx
containers:
  - name: cont1
    image: nginx:latest
```

**Image reference:**

|Image|Has default process?|Works without command?|
|---|---|---|
|`nginx`|✅ web server|✅ yes|
|`redis`|✅ redis server|✅ yes|
|`mysql`|✅ db server|✅ yes|
|`ubuntu`|❌ nothing|❌ needs `sleep infinity`|
|`busybox`|❌ nothing|❌ needs `sleep 3600`|
|`alpine`|❌ nothing|❌ needs a command|

> 💡 App images run forever on their own. Base OS images are meant to **build your own image upon** — not run directly.

---

## ⚠️ Drawbacks & Next Steps

### 🚀 The Solution: **Deployments**

```
ReplicaSet  →  manages Pods
Deployment  →  manages ReplicaSets  →  manages Pods
```

Deployments wrap ReplicaSets and add:

- 🔄 **Rolling updates** — update pods gradually with zero downtime
- ⏪ **Rollbacks** — instantly revert to a previous version
- 📊 **History** — track all revisions

> ➡️ **Next topic:** `Deployments` — the recommended way to run stateless applications in Kubernetes.

---

## 🐧 Linux Essentials for K8s

### `.bashrc`

`.bashrc` is a shell script that **automatically runs every time you open a new terminal**.

```
You open terminal → bash starts → .bashrc runs → your session is ready
```

```bash
~/.bashrc                 # location (hidden file, use ls -a to see it)
cat ~/.bashrc             # view it
vi ~/.bashrc              # edit it
source ~/.bashrc          # apply changes WITHOUT restarting terminal
```

**What to put in `.bashrc`:**

```bash
# kubectl + kops PATH (so binaries in /usr/local/bin are found)
export PATH=$PATH:/usr/local/bin

# kubectl autocomplete
source <(kubectl completion bash)

# kops autocomplete
source <(kops completion bash)

# Aliases — shortcuts
alias k="kubectl"
alias kgp="kubectl get pods"
alias kgs="kubectl get svc"
alias kgn="kubectl get nodes"
alias kgrs="kubectl get rs"
alias cls="clear"
alias kubectl="kubecolor"     # colorized output

# AWS + kops (for multi-cluster)
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
export AWS_REGION="us-east-1"
export KOPS_STATE_STORE="s3://your-kops-bucket"
```

**Config file comparison:**

|File|When it runs|
|---|---|
|`~/.bashrc`|Every new terminal ← use this|
|`~/.bash_profile`|Login only (SSH, system login)|
|`/etc/bashrc`|All users on the system|
|`~/.bash_history`|Stores command history|

---

### `export PATH` Explained

```bash
export PATH=$PATH:/usr/local/bin
#  │      │     │        │
#  │      │     │        └── new directory to add
#  │      │     └── keep everything already in PATH ($PATH = current value)
#  │      └── the variable (tells shell WHERE to look for commands)
#  └── share this with all child processes
```

**What PATH does:**

```bash
# When you type: kubectl
# Shell searches PATH directories one by one:
/usr/local/bin   ← is kubectl here? YES → run it ✅
/usr/bin         ← checked next if not found above
/bin             ← and so on...
# Not found anywhere → "command not found" ❌

echo $PATH       # see your current PATH
# /usr/local/sbin:/usr/local/bin:/usr/bin:/bin  ← colon-separated
```

**Why not just `/usr/local/bin`?**

```bash
# ❌ BAD — wipes ALL existing commands
export PATH="/usr/local/bin"
ls      # command not found ❌

# ✅ GOOD — keeps old + adds new
export PATH="$PATH:/usr/local/bin"
ls      # still works ✅
kops    # now also works ✅
```

**Why in `.bashrc` and not just the terminal?**

```bash
# Terminal only → gone tomorrow
export PATH=$PATH:/usr/local/bin   # works now, lost after close ❌

# In .bashrc → permanent
# Today ✅  Tomorrow ✅  After reboot ✅
```

**For kops specifically:**

```
Install kops → saved at /usr/local/bin/kops
                    ↓
    Shell doesn't know to look in /usr/local/bin
                    ↓
  Add to PATH in .bashrc → permanent fix
                    ↓
    Every terminal → type 'kops' → works ✅
```

---

### 📝 Vim Indentation

#### Shift LEFT

```
v → select lines → <
```

#### Shift RIGHT

```
v → select lines → >
```

#### Set exact 1-space shift

```bash
:set shiftwidth=1    # < and > now move exactly 1 space
```

#### Precise 1 space LEFT (Visual Block)

```
Ctrl+v → select lines → d    (deletes exactly 1 character)
```

#### Precise 1 space RIGHT (Visual Block)

```
Ctrl+v → select lines → I → Space → Esc
```

#### Full Cheatsheet

|Action|Command|
|---|---|
|Shift current line left|`<<`|
|Shift current line right|`>>`|
|Shift selected lines left|`v` → select → `<`|
|Shift selected lines right|`v` → select → `>`|
|Exact 1 space left|`Ctrl+v` → select → `d`|
|Exact 1 space right|`Ctrl+v` → select → `I` → Space → `Esc`|
|Repeat last action|`.`|
|Set shift size|`:set shiftwidth=1`|
|Find & replace in file|`:%s/old/new/g`|

#### Recommended `~/.vimrc` for YAML

```bash
autocmd FileType yaml setlocal shiftwidth=2 tabstop=2 expandtab
```

---

---

## 🚀 Deployments

### What is a Deployment?

A Deployment is a **higher-level resource** that manages ReplicaSets, which in turn manage Pods.

```
Deployment  →  creates ReplicaSet  →  creates Pods
```

> Key advantage over ReplicaSet: you can **update, rollback, and deploy with zero downtime**.

### Setup

```bash
# Delete old ReplicaSet first
kubectl delete rs ib-rs
kubectl get rs
kubectl get pods

# Copy replicaset.yml as a starting point
cp replicaset.yml deployment.yml

# Edit: change kind and name only
vi deployment.yml
```

### Deployment Manifest

```yaml
# deployment.yml
apiVersion: apps/v1
kind: Deployment          # ← changed from ReplicaSet
metadata:
  name: ib-deployment     # ← new name
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
```

### Deployment Commands

```bash
kubectl create -f deployment.yml        # Create
kubectl get deploy                       # List deployments
kubectl get deployments
kubectl get deploy -o wide              # With details
kubectl describe deploy ib-deployment   # Inspect
kubectl get rs                          # Deployment auto-creates a RS
kubectl get pods                        # Pods created by the RS

# Delete
kubectl delete deploy ib-deployment
```

### Scaling

```bash
# Scale OUT
kubectl scale deploy/ib-deployment --replicas=10

# Scale IN
kubectl scale deploy/ib-deployment --replicas=5
```

---

### Rolling Updates

> ⚡ This is what makes Deployment better than ReplicaSet — **live image updates with zero downtime**.

```bash
# Watch pods in a second terminal
kubectl get po --watch

# Update the image (opens vim editor)
kubectl edit deploy/ib-deployment      # change image value

# Watch: new pods come up FIRST, then old ones terminate — no downtime ✅
```

```bash
# Verify the new image is running
kubectl describe pods | grep -i image

# See all events in order
kubectl get events --sort-by=.metadata.creationTimestamp
```

**What happens during a rolling update:**

```
Pod 1 (old) running   → new Pod 1 created → old Pod 1 deleted
Pod 2 (old) running   → new Pod 2 created → old Pod 2 deleted
Pod 3 (old) running   → new Pod 3 created → old Pod 3 deleted
                                      ↑ one by one, no downtime
```

---

### Rollback

```bash
# See rollout history (all revisions)
kubectl rollout history deploy/ib-deployment

# Undo — go back to the previous image/version
kubectl rollout undo deploy/ib-deployment

# Watch pods being replaced with previous image
kubectl get pods

# Verify previous image is back
kubectl describe pods | grep -i image

# Check rollout status
kubectl rollout status deploy/ib-deployment
```

#### Pause & Resume Rollout

```bash
# Pause — locks the deployment (no undo/rollout possible)
kubectl rollout pause deploy/ib-deployment

# While paused — this will NOT work:
kubectl rollout undo deploy/ib-deployment   # ❌ blocked

# Resume — unlock
kubectl rollout resume deploy/ib-deployment

# Now undo works again
kubectl rollout undo deploy/ib-deployment   # ✅
```

---

### Deployment Strategies

|Strategy|How it works|Downtime?|
|---|---|---|
|**Rolling Update**|Delete one pod, create one — one by one|❌ None (default)|
|**Recreate**|Delete ALL pods, then create all new|✅ Yes — not recommended|
|**Blue-Green**|Run old (🟦 blue) + new (🟩 green) side by side, switch traffic when ready|❌ None|
|**Canary**|Roll out new version to small % of users, then increase gradually|❌ None|

#### Rolling Update (Default — no config needed)

```yaml
spec:
  strategy:
    type: RollingUpdate    # default, can omit this block entirely
```

#### Recreate Strategy

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ib-deployment
  labels:
    app: bank
spec:
  replicas: 3
  strategy:
    type: Recreate          # ← all pods deleted, then recreated
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
```

---

### Exposing Ports

```yaml
spec:
  containers:
  - name: cont1
    image: trainerreyaz/ib-image:latest
    ports:
    - containerPort: 8080   # port inside the container
      hostPort: 8080        # port on the host/node machine
```

|Field|Meaning|
|---|---|
|`containerPort`|Port the app listens on **inside** the container|
|`hostPort`|Maps container port directly to the **host node's** port|

> ⚠️ **`hostPort` limitations:**
> 
> - Only works on the node where the pod runs
> - If multiple pods run on same node, they **cannot share** the same hostPort
> - ✅ **Recommended instead:** Use a Kubernetes **Service** (NodePort or LoadBalancer) to expose your deployment properly

---

## ⚙️ Kubernetes Jobs

A **Job** runs a task to **completion** — unlike Deployments which run forever. Perfect for one-time or batch tasks.

|Use case|Example|
|---|---|
|Database migrations|Run once, then done|
|Backups|Triggered on demand|
|Batch data processing|Split data into chunks, process in parallel|

### Types of Jobs

|Type|Description|
|---|---|
|**Non-Parallel**|One pod at a time, runs sequentially|
|**Parallel Fixed Count**|Multiple pods run simultaneously until N completions|
|**Parallel Work Queue**|Pods scale dynamically, no fixed completion count|

### `restartPolicy` for Jobs

|Value|Behaviour|Use with|
|---|---|---|
|`Always`|Always restart on exit|Deployments, ReplicaSets|
|`OnFailure`|Restart only on error (non-zero exit)|Jobs, CronJobs|
|`Never`|Never restart, even on failure|Jobs, CronJobs (one-time)|

---

### 1. Non-Parallel Jobs

Pods execute **one by one** (sequentially).

```yaml
# nonparallel.yml
apiVersion: batch/v1
kind: Job
metadata:
  name: non-parallel-job
spec:
  completions: 3              # run 3 pods total, one at a time
  template:
    metadata:
      name: non-parallel-pod
    spec:
      containers:
      - name: non-parallel-container
        image: busybox
        command: ["echo", "Hello from the non-parallel job"]
      restartPolicy: Never
```

```bash
kubectl apply -f nonparallel.yml

kubectl get pods                             # watch pods run one by one
kubectl get jobs                             # check job status
kubectl logs -l job-name=non-parallel-job    # logs of all pods
kubectl logs non-parallel-job-2vvf9          # logs of specific pod
kubectl delete job non-parallel-job
```

> 💡 **BusyBox:** A tiny ~1MB image with built-in Unix tools (`sh`, `ls`, `echo`, `wget`, etc). Ideal for lightweight jobs and sidecars.

---

### 2. Parallel Jobs with Fixed Completion Count

Multiple pods run **simultaneously** until the total completion count is reached.

```yaml
# parallelfixed.yml
apiVersion: batch/v1
kind: Job
metadata:
  name: parallel-fixed-count-job
spec:
  completions: 6              # total pods that must succeed
  parallelism: 3              # run 3 at the same time
  template:
    metadata:
      name: parallel-fixed-count-pod
    spec:
      containers:
      - name: parallel-fixed-count-container
        image: busybox
        command: ["echo", "Hello from the parallel-fixed-count job"]
      restartPolicy: Never
```

```bash
kubectl apply -f parallelfixed.yml
kubectl get pods     # see 3 pods running simultaneously
kubectl get jobs
kubectl logs -l job-name=parallel-fixed-count-job
kubectl delete job parallel-fixed-count-job
```

**Execution flow:**

```
Round 1: Pod1 + Pod2 + Pod3 run simultaneously  (parallelism=3)
              ↓ all complete
Round 2: Pod4 + Pod5 + Pod6 run simultaneously
              ↓ all complete
Job done! (completions=6 reached)
```

---

### 3. Parallel Jobs with Work Queue

No fixed completion count — pods run in parallel and stop when work is done.

```yaml
# parallel-work-queue-job.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: parallel-work-queue-job
spec:
  parallelism: 3              # run 3 pods simultaneously, no fixed total
  template:
    metadata:
      name: parallel-work-queue-pod
    spec:
      containers:
      - name: parallel-work-queue-container
        image: busybox
        command: ["echo", "Hello from the parallel-work-queue job"]
      restartPolicy: Never
```

```bash
kubectl apply -f parallel-work-queue-job.yaml
kubectl get pods
kubectl delete job parallel-work-queue-job
```

### Jobs Comparison Table

|Feature|Non-Parallel|Parallel Fixed|Work Queue|
|---|---|---|---|
|`completions`|Required|Required|Not set|
|`parallelism`|Not set|Required|Required|
|Pods at a time|1|N (parallelism)|N (parallelism)|
|Use case|One-time task|Batch chunks|Dynamic workload|

---

## 🕐 CronJobs

A **CronJob** schedules Jobs to run at specific times — like Linux `cron` but for Kubernetes.

```
CronJob  →  creates Job  →  creates Pod  →  runs task  →  done
```

**Common use cases:** Backups, scheduled reports, periodic data cleanup, health checks.

### CronJob Manifest

```yaml
# cron.yml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello-cronjob
spec:
  schedule: "*/1 * * * *"    # runs every 1 minute
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello-container
            image: busybox
            command: ["sh", "-c", "echo Hello from Kubernetes!"]
          restartPolicy: Never
```

### Cron Schedule Syntax

```
"*/1 * * * *"
  │  │ │ │ └── day of week (0-7, Sun=0)
  │  │ │ └──── month (1-12)
  │  │ └────── day of month (1-31)
  │  └──────── hour (0-23)
  └─────────── minute (0-59)
```

|Schedule|Meaning|
|---|---|
|`*/1 * * * *`|Every minute|
|`0 * * * *`|Every hour|
|`0 0 * * *`|Every day at midnight|
|`0 9 * * 1`|Every Monday at 9am|
|`0 0 1 * *`|First day of every month|

### CronJob Commands

```bash
kubectl apply -f cron.yml

kubectl get cronjobs                    # list all cronjobs
kubectl describe cronjob hello-cronjob  # inspect

kubectl get pods                        # get latest pod name
kubectl logs <POD_NAME>                 # view logs of last run

kubectl delete cronjob hello-cronjob    # delete
```

### Job vs CronJob vs Deployment

|Feature|Deployment|Job|CronJob|
|---|---|---|---|
|Runs forever|✅|❌|❌|
|Runs once|❌|✅|❌|
|Runs on schedule|❌|❌|✅|
|Self-healing|✅|❌|❌|
|Use case|Web servers, APIs|Migrations, backups|Scheduled tasks|

---

> ⚠️ **Note:** Minikube runs on a **single node** — if that node goes down, all pods are lost. For production multi-node clusters, use **KOPS**.

---

## ☁️ KOPS — Multi-Node Cluster on AWS

### What is KOPS?

KOPS (Kubernetes Operations) is a tool to create, manage, and delete **production-grade Kubernetes clusters on AWS**.

```
Minikube  →  single node, local only, learning/dev
KOPS      →  multi-node, AWS, production-grade
```

|Tool|Used for|
|---|---|
|`kops`|Cluster-level activities (create, scale, delete cluster)|
|`kubectl`|Resource-level activities (pods, deployments, services)|

---

### Setup

#### Requirements

```
- Amazon Linux 2023, t3.micro
- IAM Role attached to EC2 with Admin permissions
- SSH key pair
```

#### Installation Script

```bash
# vi kops.sh

# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

# Install kops
wget https://github.com/kubernetes/kops/releases/download/v1.32.0/kops-linux-amd64

# Make executable and move to PATH
chmod +x kops-linux-amd64 kubectl
mv kubectl /usr/local/bin/kubectl
mv kops-linux-amd64 /usr/local/bin/kops

# Create S3 bucket for kops state store
aws s3api create-bucket \
  --bucket reyaz-kops-testbkt143333.k8s.local \
  --region ap-south-1 \
  --create-bucket-configuration LocationConstraint=ap-south-1

# Enable versioning on the bucket
aws s3api put-bucket-versioning \
  --bucket reyaz-kops-testbkt143333.k8s.local \
  --region ap-south-1 \
  --versioning-configuration Status=Enabled

# Set state store env variable
export KOPS_STATE_STORE=s3://reyaz-kops-testbkt143333.k8s.local

# Create the cluster
kops create cluster \
  --name=reyaz.k8s.local \
  --zones=ap-south-1a,ap-south-1b \
  --control-plane-count=1 \
  --control-plane-size=t3.medium \
  --node-count=2 \
  --node-size=t3.small \
  --node-volume-size=40 \
  --control-plane-volume-size=40 \
  --ssh-public-key=my-keypair.pub \
  --image=ami-02d26659fd82cf299 \
  --networking=calico

# Apply the cluster
kops update cluster --name reyaz.k8s.local --yes --admin
```

```bash
# Add to .bashrc (permanent)
export PATH=$PATH:/usr/local/bin/
export KOPS_STATE_STORE=s3://reyaz-kops-testbkt143333.k8s.local
source .bashrc

# Generate SSH key
ssh-keygen
cp /root/.ssh/id_rsa.pub my-keypair.pub
chmod 777 my-keypair.pub

# Wait for cluster to be ready (up to 10 min)
kops validate cluster --wait 10m
```

---

### Cluster Verification

```bash
kops get cluster                  # list clusters
kubectl get nodes                 # list nodes
kubectl get no                    # shorthand
kubectl get nodes -o wide         # with IPs and roles

# kubectl config — certificates and keys for authentication
cd /root/.kube/
cat config
```

> 💡 **Master node will NOT host pods** — Kubernetes schedules pods only on **worker nodes**. Pods spread equally across all worker nodes.

---

### Deploying on Multi-Node Cluster

```bash
kubectl create -f deployment.yml
kubectl get pods -o wide    # see which NODE each pod runs on
```

```
NAME                    NODE
ib-deployment-xxx-aaa   worker-node-1
ib-deployment-xxx-bbb   worker-node-2
ib-deployment-xxx-ccc   worker-node-1
ib-deployment-xxx-ddd   worker-node-2   ← spread equally ✅
```

---

### Cluster Admin Activities

> `ig` = **instance group** — a group of EC2 instances (nodes) of the same type

#### Scale Out Worker Nodes

```bash
# Edit worker node instance group
kops edit ig --name=reyaz.k8s.local nodes-ap-south-1a
# Change: maxSize = 4, minSize = 4

# Apply changes
kops update cluster --name reyaz.k8s.local --yes --admin

# Rolling update (applies to running nodes)
kops rolling-update cluster --yes

# Verify in AWS Console — 2 new worker nodes created
kubectl get nodes    # may take a few minutes
```

#### Scale Out Master Nodes

```bash
kops edit ig --name=reyaz.k8s.local master-ap-south-1a
# Change: maxSize = 2, minSize = 2

kops update cluster --name reyaz.k8s.local --yes --admin
kops rolling-update cluster --yes

kubectl get nodes    # new master node appears
```

#### Scale Pods Across Nodes

```bash
# Scale out pods
kubectl scale deploy/mb-deployment --replicas=10
kubectl get pods -o wide    # pods now spread across all 4 worker nodes

# Scale in pods
kubectl scale deploy/mb-deployment --replicas=4
kubectl get pods -o wide
```

---

### KOPS Troubleshooting

```bash
# If kubelet fails — reload systemd
sudo systemctl daemon-reload
sudo systemctl restart kubelet

# If kubelet not found in systemd — add service file manually
sudo nano /etc/systemd/system/kubelet.service
# Add:
# [Service]
# ExecStart=/usr/bin/kubelet
# Restart=always
# RestartSec=10
# [Install]
# WantedBy=multi-user.target

sudo systemctl daemon-reload
sudo systemctl enable kubelet
sudo systemctl restart kubelet

# Check kubelet logs
sudo journalctl -u kubelet -f
```

### Delete Cluster

```bash
kops delete cluster --name reyaz.k8s.local --yes
```

> ⚠️ Always delete the cluster when done — running EC2 instances cost money!

---

### KOPS Quick Reference

```bash
kops get cluster                                         # list clusters
kops validate cluster --wait 10m                         # wait for ready
kops edit ig --name=<cluster> <instance-group>           # edit node group
kops update cluster --name <cluster> --yes --admin       # apply changes
kops rolling-update cluster --yes                        # rolling update nodes
kops delete cluster --name <cluster> --yes               # delete cluster
```

---

## 🗂️ Namespaces

### What is a Namespace?

A Namespace is an **isolated virtual cluster** inside a real Kubernetes cluster. Instead of creating an expensive separate cluster for each team, you create Namespaces.

```
One Cluster
├── Namespace: dev    ← dev team's pods (isolated)
├── Namespace: prod   ← prod team's pods (isolated)
└── Namespace: qa     ← qa team's pods (isolated)
```

> Teams cannot see each other's pods. Each namespace is its own world.

---

### Built-in Namespaces

|Namespace|Purpose|
|---|---|
|`default`|Where all objects go if you don't specify a namespace|
|`kube-system`|Kubernetes internal components (api-server, scheduler, controller, kube-proxy)|
|`kube-public`|Public objects visible to all users|
|`kube-node-release`|Stores objects moved between namespaces|

> 💡 Every Kubernetes component (api-server, scheduler, etc.) runs as a **Pod** inside `kube-system` namespace.

---

### Namespace Commands

```bash
# List namespaces
kubectl get namespace
kubectl get ns

# List pods in current namespace
kubectl get pods

# List ALL pods from ALL namespaces
kubectl get pods -A
kubectl get pods --all-namespaces

# List pods in a specific namespace
kubectl get pods -n default
kubectl get pods -n kube-system      # see k8s internal pods
kubectl get pods -n kube-public      # empty
kubectl get pods -n kube-node-release # empty
```

---

### Creating & Switching Namespaces

```bash
# Check current namespace
kubectl config view    # if no namespace shown → you're in 'default'

# Create namespaces
kubectl create ns dev
kubectl create ns prod
kubectl get ns

# Switch to dev namespace
kubectl config set-context --current --namespace=dev
kubectl config view    # now shows 'dev'

# Switch to prod namespace
kubectl config set-context --current --namespace=prod

# Switch back to default
kubectl config set-context --current --namespace=default
```

---

### Working with Pods Across Namespaces

```bash
# Create pods in dev
kubectl config set-context --current --namespace=dev
kubectl run dev1 --image nginx
kubectl run dev2 --image nginx
kubectl run dev3 --image nginx
kubectl get pods    # only shows dev pods

# Create pods in prod
kubectl config set-context --current --namespace=prod
kubectl run prod1 --image nginx
kubectl run prod2 --image nginx
kubectl run prod3 --image nginx
kubectl get pods    # only shows prod pods

# View pods from any namespace
kubectl get pods -n default
kubectl get pods -n dev
kubectl get pods -n prod

# Delete specific pod in a namespace
kubectl delete pod dev1 -n dev
kubectl delete pod prod1 -n prod

# Delete ALL pods in current namespace
kubectl delete pod --all

# Delete entire namespace (deletes everything inside it)
kubectl delete namespace dev
```

> ⚠️ Currently, anyone can create/delete pods in any namespace. To restrict access properly, use **RBAC (Role-Based Access Control)**.

---

## 🖼️ Image Pull Policy

`imagePullPolicy` tells Kubernetes **when to pull (download) a container image** from the registry (DockerHub).

### Policy Options

|Policy|Behaviour|
|---|---|
|`Always`|Pull fresh image every time container starts|
|`IfNotPresent`|Use cached image if already on node; pull only if missing|
|`Never`|Never pull — image must already exist on the node|

### Default Behaviour (Important!)

|Image Tag|Default Policy|
|---|---|
|`:latest`|`Always`|
|Any specific tag (`:1.25`, `:v2`)|`IfNotPresent`|

### Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:1.25
    imagePullPolicy: IfNotPresent   # Always | IfNotPresent | Never
```

### Scenarios

| Scenario                | Result                                 |
| ----------------------- | -------------------------------------- |
| `Always`                | Pulls from registry every single start |
| `IfNotPresent`          | Uses local cache — faster startup      |
| `Never` + image missing | ❌ Pod fails with `ErrImageNeverPull`   |
| `Never` + image present | ✅ Uses local image                     |

> 💡 **Best practice:** Use specific image tags (`:1.25`) instead of `:latest` in production — it gives you predictable, consistent deployments and uses `IfNotPresent` by default, saving bandwidth.

---

<div align="center">

**☸️ Kubernetes Learning Path**

`Pods` → `ReplicaSets` → `Deployments` → `Jobs & CronJobs` → `KOPS` → `Namespaces` → **`Services`** → `Ingress` → `RBAC`

</div>