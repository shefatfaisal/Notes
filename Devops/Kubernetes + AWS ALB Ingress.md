

> EKS / Kops Cluster · Mumbai Region (ap-south-1) · Insurance App Deployment

---

## Traffic Flow

```
Internet → Route 53 (k8s.reyazclouddevops.click)
         → AWS ALB (HTTPS:443 / HTTP:80)
         → ACM Cert (*.reyazclouddevops.click)
         → Ingress Rule (insurance-ingress)
         → NodePort Service (:80 → :31433)
         → Pods ×3 (containerPort 80)
```

---

## Step 1 · Verify the Cluster

```bash
kubectl get nodes
```

> Confirm all nodes are in `Ready` state before proceeding.

---

## Step 2 · Issue an ACM Certificate

1. Go to **AWS Console → ACM → Mumbai Region (ap-south-1)**
2. Request a public certificate for `*.reyazclouddevops.click` _(wildcard covers all subdomains)_
3. Complete **DNS validation** — add the CNAME record in Route 53
4. Copy the **Certificate ARN** — needed later in `ingress.yml`

---

## Step 3 · Deployment + Service Manifest

```bash
vi application-deploy-service.yml
```

### Deployment

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
          ports:
            - containerPort: 80
          # Health Probes
          livenessProbe:
            httpGet:
              path: /
              port: 80
            initialDelaySeconds: 15   # Wait 15s before first check
            periodSeconds: 10         # Check every 10s
            failureThreshold: 3       # Restart after 3 failed checks

          readinessProbe:
            httpGet:
              path: /
              port: 80
            initialDelaySeconds: 5    # Start checking after 5s
            periodSeconds: 5          # Check every 5s
            failureThreshold: 2       # Mark unready after 2 failed checks

          startupProbe:
            httpGet:
              path: /
              port: 80
            failureThreshold: 30      # Give app up to 30 failures
            periodSeconds: 10         # before considering startup failed
```

### Health Probes

> All three probes hit `GET /` on port 80.

|Probe|Purpose|Initial Delay|Period|Failure Threshold|Action|
|---|---|---|---|---|---|
|**livenessProbe**|Is the container alive?|15s|10s|3|Restart pod|
|**readinessProbe**|Is the container ready for traffic?|5s|5s|2|Mark unready|
|**startupProbe**|Did the app finish starting?|—|10s|30 (= 5 min max)|Fail container|


### NodePort Service _(same file, separated by `---`)_

```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: insurance-service
spec:
  type: NodePort
  selector:
    app: bank
  ports:
    - port: 80
      targetPort: 80
      nodePort: 31433
```

### Apply the manifest

```bash
kubectl apply -f application-deploy-service.yml
```

> **IAM Note:** Search for the role `nodes.reyaz.k8s.local` and attach **admin or ACM permissions** — the Load Balancer Controller needs this to provision ALBs on AWS.

---

## Step 4 · Install cert-manager

> Required by the AWS Load Balancer Controller to handle TLS certificates.

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.12.3/cert-manager.yaml

kubectl rollout status deployment/cert-manager -n cert-manager
```

---

## Step 5 · Install Helm (Package Manager)

> Helm is a package manager for Kubernetes — like `apt` or `yum` but for K8s apps.

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
helm version
```

---

## Step 6 · Install AWS Load Balancer Controller via Helm

> Kubernetes does **not** include an ALB controller by default. We install it from the EKS chart repo.

```bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update

# Note: replace clusterName with your actual cluster name
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=reyaz.k8s.local \
  --set serviceAccount.create=true \
  --set hostNetwork=true
```

### Verify the controller pods are running

```bash
kubectl get pods -n kube-system | grep aws-load-balancer
```

---


## Before that search for the role that is attached to the node instance of the cluster and give that role an admin permission





## Concept: What is an Ingress Controller?

An **Ingress Controller** is a smart **Layer-7 load balancer** that manages HTTP/HTTPS traffic coming from _outside_ the cluster and routes it to the appropriate services _inside_ the cluster — based on **host-based** or **path-based** routing rules.

### Two Types

|Type|What it does|
|---|---|
|**NGINX Ingress Controller**|Configures an Nginx reverse proxy _inside_ the cluster. Works anywhere (on-prem, any cloud).|
|**AWS ALB Ingress Controller**|Provisions an AWS Application Load Balancer per Ingress resource. Native AWS integration, TLS via ACM.|

> We use the **AWS ALB Ingress Controller** for this project.

### Mental Model — Kubernetes as a Gated Community 🏘️

```
Each Pod        = A house
Each Service    = A street (groups certain houses together)
Ingress Ctrl    = Security gate + receptionist who:
                    → Accepts all visitors (HTTP/HTTPS requests)
                    → Reads the "address" (host/path)
                    → Directs visitor to the correct street (Service)
```

---

## Step 7 · Ingress Manifest

> **Before applying:** replace the ACM ARN and host with your own values.

```bash
vi ingress.yml
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: insurance-ingress
  annotations:
    # Expose the ALB to the internet
    alb.ingress.kubernetes.io/scheme: internet-facing

    # Route traffic to EC2 node instances (NodePort)
    alb.ingress.kubernetes.io/target-type: instance

    # Auto-discover subnets from kops tags
    alb.ingress.kubernetes.io/subnet-auto-discovery: kops-util

    # Redirect all HTTP → HTTPS
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP":80},{"HTTPS":443}]'
    alb.ingress.kubernetes.io/ssl-redirect: "443"

    # ACM Certificate ARN for SSL — replace this!
    alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:us-east-2:794267022248:certificate/9fb38654-5cac-4b15-ac66-2f5798b2accc

    # Optional: Health check settings for ALB
    alb.ingress.kubernetes.io/healthcheck-path: /
    alb.ingress.kubernetes.io/healthcheck-port: traffic-port

spec:
  ingressClassName: alb
  rules:
    - host: shefat.web.lovelu.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: insurance-service
                port:
                  number: 80
```

---

## Step 8 · Apply and Verify

```bash
kubectl apply -f ingress.yml

kubectl get pods                          # confirm 3/3 Running
kubectl get ing                           # shows ALB DNS after ~60s
kubectl describe ing insurance-ingress    # detailed events & ALB ARN
```

---

## Step 9 · DNS — Route 53

1. Copy the **ALB DNS name** from `kubectl get ing`
2. In **Route 53 → your hosted zone** → create an **A record (Alias)**
    - Name: `k8s.reyazclouddevops.click`
    - Alias target: the ALB DNS name
3. Visit `https://k8s.reyazclouddevops.click` — HTTPS is now enforced via the ACM wildcard cert ✅

---

## Quick Reference — Key Commands

|What|Command|
|---|---|
|Check nodes|`kubectl get nodes`|
|Apply Deployment + Service|`kubectl apply -f application-deploy-service.yml`|
|Check LB controller pods|`kubectl get pods -n kube-system \| grep aws-load-balancer`|
|Apply Ingress|`kubectl apply -f ingress.yml`|
|Get Ingress + ALB DNS|`kubectl get ing`|
|Describe Ingress events|`kubectl describe ing insurance-ingress`|

---

## Summary — Why This Architecture?

Without an Ingress Controller, every Service needs its own Load Balancer = expensive and hard to manage.

With **one ALB + Ingress Controller**:

- ✅ One load balancer handles all external traffic
- ✅ SSL terminates at the ALB (via ACM — no cert management on pods)
- ✅ HTTP is auto-redirected to HTTPS
- ✅ Path/host routing to multiple services from a single entry point
- ✅ Health checks handled at the ALB level