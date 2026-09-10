
roboshop user argocd deployment:
-----------------------------------

Please clone below repository user-argocd 

`https://github.com/iam-vanimina/user-argocd.git `



`cd  /user-argocd`

Make changes helm values as per your tags or version or image url etc ..

Make changes in application.yaml (mention your github repo url and create k8s roboshop namespace )

`kubectl apply -f application.yaml  `

In my case my github repo is 

`https://github.com/iam-vanimina/user-argocd.git `

github repo act as the truth for the argocd.

-------------------------------------------------------------------------------------------------------------------------

## 🔴 Argo CD Application — User Service

The RoboShop **User Service** is deployed to Kubernetes using **Argo CD + Helm** following a GitOps deployment model.

### 📄 Application Configuration

```yaml
project: roboshop

source:
  repoURL: https://github.com/iam-vanimina/user-argocd.git
  path: .
  targetRevision: main

  helm:
    valueFiles:
      - values.yaml
    releaseName: user

destination:
  server: https://kubernetes.default.svc
  namespace: roboshop

syncPolicy:
  automated:
    prune: true
    selfHeal: true

  syncOptions:
    - CreateNamespace=true
    - ApplyOutOfSyncOnly=true
    - ServerSideApply=true
    - PruneLast=true
```

### 🔄 GitOps Deployment Flow

```text
                    🐙 GitHub
                       │
                       │ main
                       ▼
          ┌─────────────────────────┐
          │     user-argocd.git     │
          │                         │
          │ ├── Helm Chart          │
          │ └── values.yaml         │
          └────────────┬────────────┘
                       │
                       │ GitOps
                       ▼
                 🔴 Argo CD
                       │
                       │ Helm
                       ▼
                ☸️ Kubernetes
                       │
                Namespace: roboshop
                       │
                       ▼
                  👤 User Service
```

### 📋 Configuration

| Setting            | Value                            |
| ------------------ | -------------------------------- |
| 🔴 Argo CD Project | `roboshop`                       |
| 🐙 Repository      | `user-argocd.git`                |
| 🌿 Branch          | `main`                           |
| 📁 Path            | `.`                              |
| ⛵ Deployment       | Helm                             |
| 📄 Values File     | `values.yaml`                    |
| 🚀 Release Name    | `user`                           |
| ☸️ Cluster         | `https://kubernetes.default.svc` |
| 📦 Namespace       | `roboshop`                       |
| 🔄 Automated Sync  | Enabled                          |
| 🧹 Auto Prune      | Enabled                          |
| ❤️ Self Heal       | Enabled                          |

### 🤖 Automated Synchronization

Argo CD continuously monitors the Git repository and reconciles the Kubernetes cluster with the desired state.

```yaml
syncPolicy:
  automated:
    prune: true
    selfHeal: true
```

#### 🧹 Automatic Pruning

When a Kubernetes resource is removed from Git, Argo CD can automatically remove the corresponding resource from the cluster.

```text
🐙 Git
 │
 │ Resource removed
 ▼
🔴 Argo CD
 │
 │ Detects difference
 ▼
☸️ Kubernetes
 │
 └── 🗑️ Resource removed
```

#### ❤️ Self-Healing

If a resource is manually modified in Kubernetes, Argo CD detects the drift and restores the Git-defined configuration.

```text
☸️ Kubernetes
 │
 │ Manual change
 ▼
⚠️ Configuration Drift
 │
 ▼
🔴 Argo CD
 │
 │ Reconciliation
 ▼
✅ Desired State Restored
```

### ⚙️ Sync Options

#### 📦 Create Namespace

```yaml
- CreateNamespace=true
```

Creates the `roboshop` namespace automatically if it does not already exist.

#### ⚡ Apply Out Of Sync Only

```yaml
- ApplyOutOfSyncOnly=true
```

Only resources detected as out of sync are applied.

#### 🚀 Server-Side Apply

```yaml
- ServerSideApply=true
```

Uses Kubernetes Server-Side Apply for resource management.

#### 🧹 Prune Last

```yaml
- PruneLast=true
```

Prunes resources as a final synchronization step.

### 🏗️ User Service Architecture

```text
                         🔴 Argo CD
                              │
                              ▼
                       ⛵ Helm Release
                            "user"
                              │
                              ▼
                     ☸️ Kubernetes
                              │
                       roboshop namespace
                              │
                              ▼
                       👤 User Service
                              │
                              ▼
                         🟢 User Pod
                              │
                              ▼
                        💾 MongoDB
```

### 🎯 GitOps Source of Truth

```text
🐙 GitHub
    │
    │ Desired State
    ▼
🔴 Argo CD
    │
    │ Reconciliation
    ▼
☸️ Kubernetes
    │
    └── 👤 User Service
```

Git is the **source of truth**, while Argo CD continuously ensures that the Kubernetes environment matches the configuration stored in Git.

### 🔍 Useful Argo CD Commands

```bash
# List applications
argocd app list

# Get User application
argocd app get user

# Synchronize User application
argocd app sync user

# View application history
argocd app history user

# View deployed resources
argocd app resources user
```

### 🏆 GitOps Benefits

* ✅ Git-based source of truth
* ✅ Automated deployment
* ✅ Helm-based configuration
* ✅ Automatic synchronization
* ✅ Self-healing
* ✅ Automatic pruning
* ✅ Declarative Kubernetes management
* ✅ Version-controlled infrastructure
* ✅ Repeatable deployments

````

### 🛍️ RoboShop Microservices

You can use the same structure for each RoboShop service:

```text
🐙 GitHub
 │
 ├── frontend-argocd.git
 ├── user-argocd.git
 ├── cart-argocd.git
 ├── catalogue-argocd.git
 ├── shipping-argocd.git
 ├── payment-argocd.git
 └── dispatch-argocd.git
        │
        ▼
   🔴 Argo CD
        │
        ▼
   ☸️ Kubernetes
        │
        ▼
   📦 roboshop namespace
````

This keeps your README consistent across **Frontend, User, Cart, Catalogue, Shipping, Payment, and Dispatch** services.
------------------------------------------------------------------------------------------------------------------------

## ⚙️ User Service ConfigMap

The `user` ConfigMap contains configuration used by the RoboShop **User Service**.

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: user
  namespace: roboshop

  annotations:
    argocd.argoproj.io/tracking-id: user:/ConfigMap:roboshop/user

data:
  MONGO: "true"
```

### 📋 Configuration

| Configuration      | Value       |
| ------------------ | ----------- |
| 📦 Resource        | `ConfigMap` |
| 🏷️ Name           | `user`      |
| 📁 Namespace       | `roboshop`  |
| 🔧 Configuration   | `MONGO`     |
| 💾 MongoDB Enabled | `true`      |
| 🔴 Managed By      | Argo CD     |

### 💾 MongoDB Configuration

```yaml
data:
  MONGO: "true"
```

The `MONGO` configuration indicates that the User Service is configured to use **MongoDB**.

### 🔄 GitOps Flow

```text
🐙 GitHub
   │
   │ user-argocd
   ▼
🔴 Argo CD
   │
   ▼
☸️ Kubernetes
   │
   ├── 📦 ConfigMap
   │      user
   │
   └── 👤 User Service
          │
          ▼
       💾 MongoDB
```

### 🔗 Argo CD Tracking

```yaml
annotations:
  argocd.argoproj.io/tracking-id: user:/ConfigMap:roboshop/user
```

Argo CD uses this tracking information to associate the `user` ConfigMap with the User application.

### ⚠️ GitOps Best Practice

Do not commit Kubernetes-generated fields from the live resource:

```yaml
creationTimestamp:
resourceVersion:
uid:
```

Your Git manifest should contain the **desired configuration**, while Kubernetes manages runtime metadata.
---------------------------------------------------------------------------------------------------------------------
🔴 Argo CD Application
        │
        ▼
   📦 ConfigMap
      user
        │
        ▼
   🚀 Deployment
      user
        │
        ▼
   🔄 ReplicaSet
        │
        ▼
    🟢 Pod
        │
        ▼
   👤 User Service
        │
        ▼
    💾 MongoDB

    -------------------------------------------------------------------------------------------------------------------

    # 🔵 User Service – Kubernetes Service

The **User Service Kubernetes Service** provides internal network access to the User Service Pods inside the `roboshop` namespace.

It uses a **ClusterIP** service, making the User Service accessible only within the Kubernetes cluster.

---

## 📄 Service Manifest

```yaml
apiVersion: v1
kind: Service

metadata:
  name: user
  namespace: roboshop

  annotations:
    argocd.argoproj.io/tracking-id: user:/Service:roboshop/user

spec:
  type: ClusterIP

  ports:
    - port: 8080
      targetPort: 8080
      protocol: TCP

  selector:
    app: user
    project: roboshop
    tier: app
```

---

## ⚙️ Service Configuration

| Configuration    | Value       |
| ---------------- | ----------- |
| 🏷️ Service Name | `user`      |
| 📦 Namespace     | `roboshop`  |
| 🌐 Service Type  | `ClusterIP` |
| 🔌 Service Port  | `8080`      |
| 🎯 Target Port   | `8080`      |
| 🔗 Protocol      | `TCP`       |
| 🎯 Selector      | `app=user`  |
| 🏗️ Project      | `roboshop`  |
| 📊 Tier          | `app`       |
| 🔄 Managed By    | Argo CD     |

---

## 🔍 How It Works

The Service selects User Service Pods using these labels:

```yaml
selector:
  app: user
  project: roboshop
  tier: app
```

Traffic flow:

```text
┌──────────────────────┐
│   Other RoboShop     │
│      Services        │
└──────────┬───────────┘
           │
           │ TCP :8080
           ▼
┌──────────────────────┐
│   user Service       │
│     ClusterIP        │
│       :8080          │
└──────────┬───────────┘
           │
           │ Selector
           ▼
┌──────────────────────┐
│    User Pods         │
│                      │
│  app=user            │
│  project=roboshop    │
│  tier=app            │
│                      │
│       :8080          │
└──────────────────────┘
```

---

## 🌐 Internal DNS

Other applications inside the `roboshop` namespace can access the User Service using:

```text
user:8080
```

or the fully qualified Kubernetes DNS name:

```text
user.roboshop.svc.cluster.local:8080
```

For example:

```bash
curl http://user:8080
```

---

## 🔄 GitOps / Argo CD

The Service is managed through **Argo CD**.

```text
        GitHub Repository
               │
               ▼
        📦 user-argocd
               │
               ▼
          🔴 Argo CD
               │
               ▼
      Kubernetes Cluster
               │
               ▼
       🔵 User Service
          ClusterIP
               │
               ▼
          👤 User Pods
```

The Argo CD tracking annotation:

```yaml
argocd.argoproj.io/tracking-id: user:/Service:roboshop/user
```

allows Argo CD to associate this Kubernetes Service with the corresponding GitOps-managed resource.

---

## 🧪 Useful Commands

### Check Service

```bash
kubectl get svc user -n roboshop
```

### Detailed Service Information

```bash
kubectl describe svc user -n roboshop
```

### Check Service Endpoints

```bash
kubectl get endpoints user -n roboshop
```

### Check EndpointSlices

```bash
kubectl get endpointslice -n roboshop
```

### Check User Pods

```bash
kubectl get pods -n roboshop -l app=user
```

### Test DNS Resolution

```bash
kubectl run test-pod \
  --rm -it \
  --image=busybox \
  -n roboshop \
  -- nslookup user
```

### Test Service Connectivity

```bash
kubectl run test-pod \
  --rm -it \
  --image=curlimages/curl \
  -n roboshop \
  -- curl http://user:8080
```

---

## ⚠️ Important

Do **not** normally commit these generated fields from `kubectl get svc user -o yaml`:

```yaml
creationTimestamp:
resourceVersion:
uid:
clusterIP:
clusterIPs:
status:
```

`clusterIP` is assigned by Kubernetes automatically. Keeping it out of Git makes the manifest portable across clusters.

---

## 🏗️ RoboShop User Service Architecture

```text
                 🔴 Argo CD
                     │
                     ▼
              📦 User Service
                  Manifest
                     │
                     ▼
          ┌─────────────────────┐
          │   Kubernetes        │
          │                     │
          │  🔵 Service         │
          │     user:8080       │
          │         │           │
          │         ▼           │
          │  🟢 User Pods       │
          │         │           │
          │         ▼           │
          │   👤 User App       │
          │         │           │
          │         ▼           │
          │    💾 MongoDB       │
          └─────────────────────┘
```

### 🎯 Key Points

* 🔵 `ClusterIP` provides **internal-only** access.
* 🔌 Service listens on **port 8080**.
* 🎯 `targetPort: 8080` forwards traffic to the User Pods.
* 🏷️ Label selectors identify the User Pods.
* 🌐 Kubernetes DNS provides service discovery.
* 🔄 Argo CD manages the Service declaratively through GitOps.
* 🚫 No external LoadBalancer or NodePort is required for internal communication.
-------------------------------------------------------------------------------------------------------------------------

# 🚀 User Service – Kubernetes Deployment

The **User Service Deployment** manages the User Service application Pods in the `roboshop` namespace.

It ensures the desired number of Pods are running and provides **rolling updates** when a new container image is deployed.

---

## 📄 Deployment Manifest

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: user
  namespace: roboshop

  labels:
    app: user
    project: roboshop
    tier: app

  annotations:
    argocd.argoproj.io/tracking-id: user:apps/Deployment:roboshop/user

spec:
  replicas: 1

  selector:
    matchLabels:
      app: user
      project: roboshop
      tier: app

  strategy:
    type: RollingUpdate

    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%

  template:
    metadata:
      labels:
        app: user
        project: roboshop
        tier: app

    spec:
      containers:
        - name: user
          image: vanimina/user:1.0.0
          imagePullPolicy: Always

          envFrom:
            - configMapRef:
                name: user

          resources: {}

      restartPolicy: Always

  progressDeadlineSeconds: 600
  revisionHistoryLimit: 10
```

---

## ⚙️ Deployment Configuration

| Configuration        | Value                 |
| -------------------- | --------------------- |
| 🏷️ Deployment Name  | `user`                |
| 📦 Namespace         | `roboshop`            |
| 🔢 Replicas          | `1`                   |
| 🐳 Image             | `vanimina/user:1.0.0` |
| 🔄 Image Pull Policy | `Always`              |
| 🔄 Strategy          | `RollingUpdate`       |
| 📈 Max Surge         | `25%`                 |
| 📉 Max Unavailable   | `25%`                 |
| 🔌 Application Port  | `8080`                |
| ⚙️ Configuration     | ConfigMap `user`      |
| 🏗️ Project          | `roboshop`            |
| 🏷️ Tier             | `app`                 |
| 🔴 GitOps            | Argo CD               |

---

## 🔄 Deployment Architecture

The Deployment creates and manages a ReplicaSet, which then creates the User Service Pod.

```text
                 🔴 Argo CD
                     │
                     ▼
              🚀 Deployment
                  user
                     │
                     ▼
              🔄 ReplicaSet
                     │
                     ▼
                 🟢 Pod
                     │
                     ▼
              👤 User Container
                     │
                     ▼
              💾 MongoDB
```

The Deployment does **not** directly manage the application's container lifecycle. Kubernetes uses the following hierarchy:

```text
Deployment
    │
    └── ReplicaSet
            │
            └── Pod
                  │
                  └── Container
```

---

## 🐳 Container Configuration

The Deployment runs:

```yaml
image: vanimina/user:1.0.0
```

with:

```yaml
imagePullPolicy: Always
```

This tells Kubernetes to check the container registry for the image whenever the container is started.

The container is named:

```yaml
name: user
```

---

## ⚙️ ConfigMap Integration

The User Service loads environment variables from the `user` ConfigMap:

```yaml
envFrom:
  - configMapRef:
      name: user
```

The configuration flow is:

```text
📦 ConfigMap
   user
    │
    │ Environment Variables
    ▼
🚀 User Deployment
    │
    ▼
🟢 User Pod
    │
    ▼
👤 User Container
```

For example, the ConfigMap contains:

```yaml
data:
  MONGO: "true"
```

The exact behavior of this variable depends on how the User Service application consumes it.

---

## 🔄 Rolling Update Strategy

The Deployment uses:

```yaml
strategy:
  type: RollingUpdate
```

with:

```yaml
rollingUpdate:
  maxSurge: 25%
  maxUnavailable: 25%
```

This allows Kubernetes to update the User Service gradually instead of stopping all existing Pods at once.

For example:

```text
Current Version
     │
     ▼
🟢 user:1.0.0
     │
     │ New image deployed
     ▼
🔄 Rolling Update
     │
     ├── 🟢 Old Pod
     │
     └── 🟢 New Pod
             │
             ▼
       Health/Readiness Check
             │
             ▼
       Old Pod Removed
             │
             ▼
       🟢 New Version
```

This helps reduce application downtime during deployments.

---

## 🎯 Label Selectors

The Deployment identifies its Pods using:

```yaml
selector:
  matchLabels:
    app: user
    project: roboshop
    tier: app
```

The Pod template uses the same labels:

```yaml
labels:
  app: user
  project: roboshop
  tier: app
```

This relationship is important:

```text
Deployment Selector
       │
       │ matches
       ▼
Pod Labels
       │
       ▼
User Pods
```

The `Service` also uses these labels to route traffic to the User Pods.

---

## 🌐 User Service Integration

The User Service created earlier selects these Pods:

```yaml
selector:
  app: user
  project: roboshop
  tier: app
```

Therefore the complete traffic flow is:

```text
        Other Services
              │
              │
              ▼
      🔵 user Service
          :8080
              │
              │ Selector
              ▼
       🟢 User Pod
          :8080
              │
              ▼
       👤 User App
              │
              ▼
         💾 MongoDB
```

---

## 🔄 GitOps Flow

The User Deployment is managed by Argo CD:

```text
┌──────────────────┐
│   GitHub Repo    │
│  user-argocd     │
└────────┬─────────┘
         │
         │ Git Commit
         ▼
┌──────────────────┐
│     🔴 Argo CD   │
└────────┬─────────┘
         │
         │ Sync
         ▼
┌──────────────────┐
│   Kubernetes     │
│     Deployment   │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│    ReplicaSet    │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│    🟢 User Pod   │
└──────────────────┘
```

The Argo CD tracking annotation:

```yaml
argocd.argoproj.io/tracking-id: user:apps/Deployment:roboshop/user
```

allows Argo CD to track this Deployment as the `user` application resource.

---

## 🧪 Useful Kubernetes Commands

### Check Deployment

```bash
kubectl get deployment user -n roboshop
```

### Check Deployment Details

```bash
kubectl describe deployment user -n roboshop
```

### Check ReplicaSet

```bash
kubectl get rs -n roboshop -l app=user
```

### Check User Pods

```bash
kubectl get pods -n roboshop -l app=user
```

### Watch Deployment Rollout

```bash
kubectl rollout status deployment/user -n roboshop
```

### View Rollout History

```bash
kubectl rollout history deployment/user -n roboshop
```

### Restart Deployment

```bash
kubectl rollout restart deployment/user -n roboshop
```

### Check Deployment YAML

```bash
kubectl get deployment user -n roboshop -o yaml
```

---

## 🧹 Fields Removed From Git

The following fields from the original `kubectl get deployment -o yaml` output are generated by Kubernetes and should normally **not** be stored in your Git-managed manifest:

```yaml
creationTimestamp:
generation:
resourceVersion:
uid:
deployment.kubernetes.io/revision:
status:
observedGeneration:
availableReplicas:
readyReplicas:
updatedReplicas:
conditions:
```

These values describe the **current runtime state** rather than the desired state.

### GitOps Principle

```text
❌ Runtime State
   kubectl generated metadata/status

        ↓

✅ Desired State
   Git-managed Kubernetes manifest

        ↓

🔴 Argo CD
   continuously reconciles

        ↓

☸️ Kubernetes Cluster
```

---

## 📌 Production Recommendation

Your current Deployment has:

```yaml
resources: {}
```

For a production Kubernetes environment, consider defining CPU and memory requests/limits:

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "512Mi"
```

The exact values should be based on the User Service's actual resource usage rather than copied blindly.

---

## 🏆 User Service Kubernetes Stack

```text
              🔴 Argo CD
                   │
                   ▼
            🚀 Deployment
               user
                   │
                   ▼
             🔄 ReplicaSet
                   │
                   ▼
              🟢 Pod
                   │
          ┌────────┴────────┐
          │                 │
          ▼                 ▼
    ⚙️ ConfigMap       👤 User App
       user                 │
                            ▼
                       💾 MongoDB
          │
          ▼
    🔵 Service
       user:8080
```

### 🎯 Key Points

* 🚀 Deployment maintains the desired number of User Pods.
* 🔄 `RollingUpdate` enables controlled application updates.
* 🐳 Image: `vanimina/user:1.0.0`.
* ⚙️ Environment configuration comes from the `user` ConfigMap.
* 🏷️ Labels connect the Deployment, ReplicaSet, Pods, and Service.
* 🔵 The `user` ClusterIP Service provides internal access on port `8080`.
* 🔴 Argo CD continuously reconciles the Deployment from Git.
* 🧹 Runtime-generated metadata and status should remain outside the Git manifest.
-------------------------------------------------------------------------------------------------------------------------
# 📈 User Service – Horizontal Pod Autoscaler

The **Horizontal Pod Autoscaler (HPA)** automatically adjusts the number of User Service Pods based on CPU utilization.

For the User Service, the HPA is configured to maintain CPU utilization around **20%**, with a minimum of **1 Pod** and a maximum of **10 Pods**.

---

## 📄 HPA Manifest

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler

metadata:
  name: user
  namespace: roboshop

  annotations:
    argocd.argoproj.io/tracking-id: user:autoscaling/HorizontalPodAutoscaler:roboshop/user

spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: user

  minReplicas: 1
  maxReplicas: 10

  targetCPUUtilizationPercentage: 20
```

---

## ⚙️ HPA Configuration

| Configuration       | Value                   |
| ------------------- | ----------------------- |
| 📈 Resource         | HorizontalPodAutoscaler |
| 🏷️ Name            | `user`                  |
| 📦 Namespace        | `roboshop`              |
| 🎯 Target           | `Deployment/user`       |
| 📉 Minimum Replicas | `1`                     |
| 📈 Maximum Replicas | `10`                    |
| 🧠 Metric           | CPU Utilization         |
| 🎯 Target CPU       | `20%`                   |
| 🔴 GitOps           | Argo CD                 |
| 📡 API Version      | `autoscaling/v1`        |

---

## 🔄 How HPA Works

The HPA continuously monitors the User Service's CPU utilization and adjusts the number of replicas.

```text
                  🔴 Argo CD
                      │
                      ▼
              📈 HPA: user
                      │
                      │ monitors
                      ▼
              🚀 Deployment
                  user
                      │
                      ▼
               🟢 User Pods
                      │
                      │ CPU Usage
                      ▼
             📊 Metrics API
                      │
                      ▼
             🔄 HPA Controller
                      │
          ┌───────────┴───────────┐
          │                       │
       CPU > 20%              CPU < 20%
          │                       │
          ▼                       ▼
     ⬆️ Scale Out              ⬇️ Scale In
          │                       │
          ▼                       ▼
     More Pods                Fewer Pods
```

---

## 📊 Scaling Configuration

The HPA has:

```yaml
minReplicas: 1
maxReplicas: 10
```

Therefore:

```text
Minimum
   │
   ▼
🟢 1 Pod
   │
   │ CPU increases
   ▼
🟢🟢 2 Pods
   │
   ▼
🟢🟢🟢 3 Pods
   │
   ▼
   ...
   │
   ▼
🟢🟢🟢🟢🟢🟢🟢🟢🟢🟢
       10 Pods Maximum
```

The HPA will not scale the Deployment below **1 Pod** or above **10 Pods**.

---

## 🎯 CPU Target

The configured CPU target is:

```yaml
targetCPUUtilizationPercentage: 20
```

This means the HPA attempts to maintain average CPU utilization of the User Service Pods around **20% of their requested CPU**.

For CPU-based HPA to work correctly, CPU **requests** should be configured on the User container.

For example:

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "512Mi"
```

With a CPU request of `100m`:

```text
100m CPU request
      │
      ▼
20% target
      │
      ▼
~20m average CPU utilization
```

The actual scaling behavior depends on the metrics reported by Kubernetes and the HPA controller.

---

## ⚠️ Current Metrics Issue

Your original HPA output contains:

```text
FailedGetResourceMetric
```

and:

```text
unable to get metrics for resource cpu
```

with:

```text
the server could not find the requested resource
(get pods.metrics.k8s.io)
```

This indicates that the Kubernetes cluster currently cannot retrieve CPU metrics through the **Metrics API**.

Therefore, although the HPA exists, it cannot currently calculate CPU-based scaling correctly.

### 🔍 Check Metrics Server

Run:

```bash
kubectl get deployment metrics-server -n kube-system
```

Check the Metrics API:

```bash
kubectl get apiservice v1beta1.metrics.k8s.io
```

Check whether Pod metrics are available:

```bash
kubectl top pods -n roboshop
```

Check Node metrics:

```bash
kubectl top nodes
```

If `kubectl top` returns CPU and memory usage, the Metrics API is generally available.

---

## 🧪 HPA Commands

### Check HPA

```bash
kubectl get hpa user -n roboshop
```

### Detailed HPA Information

```bash
kubectl describe hpa user -n roboshop
```

### Watch HPA

```bash
kubectl get hpa user -n roboshop -w
```

### Check CPU Usage

```bash
kubectl top pods -n roboshop -l app=user
```

### Check Deployment Replicas

```bash
kubectl get deployment user -n roboshop
```

### Check HPA YAML

```bash
kubectl get hpa user -n roboshop -o yaml
```

---

## 🔗 HPA → Deployment Relationship

The HPA targets:

```yaml
scaleTargetRef:
  apiVersion: apps/v1
  kind: Deployment
  name: user
```

Therefore:

```text
📈 HPA
 user
  │
  │ scaleTargetRef
  ▼
🚀 Deployment
 user
  │
  ▼
🔄 ReplicaSet
  │
  ▼
🟢 User Pods
```

The HPA changes the **replica count of the Deployment**. The Deployment then creates or removes Pods through its ReplicaSet.

---

## 🔴 GitOps with Argo CD

The HPA is managed by Argo CD using the tracking annotation:

```yaml
argocd.argoproj.io/tracking-id: user:autoscaling/HorizontalPodAutoscaler:roboshop/user
```

GitOps flow:

```text
       📦 GitHub
           │
           │ Git Commit
           ▼
       🔴 Argo CD
           │
           │ Sync
           ▼
   📈 HPA: user
           │
           ▼
   🚀 Deployment: user
           │
           ▼
      🟢 User Pods
           │
           ▼
    📊 CPU Metrics
           │
           ▼
    🔄 HPA Controller
           │
           ├──── ⬆️ Scale Out
           │
           └──── ⬇️ Scale In
```

---

## 🧹 Fields Removed From Git

The following fields from the original output are runtime-generated and should not normally be committed:

```yaml
creationTimestamp:
resourceVersion:
uid:
status:
currentReplicas:
desiredReplicas:
```

The following annotations are also runtime-generated HPA status information and should be removed:

```yaml
autoscaling.alpha.kubernetes.io/conditions:
autoscaling.alpha.kubernetes.io/current-metrics:
```

Your Git manifest should describe the **desired HPA configuration**, not the current runtime state.

---

## 🏗️ Complete User Service Scaling Architecture

```text
                    🔴 Argo CD
                        │
          ┌─────────────┴─────────────┐
          │                           │
          ▼                           ▼
   📦 ConfigMap                 📈 HPA
      user                       user
          │                           │
          │                           │
          ▼                           ▼
   🚀 Deployment ◄──────────── Scaling Decision
      user
          │
          ▼
     🔄 ReplicaSet
          │
     ┌────┼────┐
     │    │    │
     ▼    ▼    ▼
    🟢   🟢   🟢
   Pod  Pod  Pod
     │    │    │
     └────┼────┘
          │
          ▼
      👤 User App
          │
          ▼
      💾 MongoDB
```

### 🏆 Key Points

* 📈 HPA automatically adjusts User Service replicas.
* 🎯 CPU target is **20%**.
* 📉 Minimum replicas: **1**.
* 📈 Maximum replicas: **10**.
* 🚀 HPA scales the `user` Deployment.
* 📊 CPU metrics are required for CPU-based scaling.
* ⚠️ Your current cluster output indicates the Metrics API is unavailable or not providing Pod CPU metrics.
* 🔴 Argo CD manages the HPA declaratively through GitOps.
* ⚙️ CPU requests should be configured for meaningful CPU-utilization-based HPA behavior.
---------------------------------------------------------------------------------------------------------------------

# 🔄 User Service – ReplicaSet

The **ReplicaSet** ensures that the desired number of User Service Pods are running.

In this deployment, the ReplicaSet maintains **1 User Pod** and is automatically created and managed by the `user` Deployment.

> ⚠️ **GitOps Note:** ReplicaSets are normally generated by Deployments. You generally do **not** need to maintain this ReplicaSet YAML separately in Git.

---

## 📄 Clean ReplicaSet Manifest

```yaml
apiVersion: apps/v1
kind: ReplicaSet

metadata:
  name: user-58f6dfd8f
  namespace: roboshop

  labels:
    app: user
    project: roboshop
    tier: app

  annotations:
    argocd.argoproj.io/tracking-id: user:apps/Deployment:roboshop/user

spec:
  replicas: 1

  selector:
    matchLabels:
      app: user
      project: roboshop
      tier: app
      pod-template-hash: 58f6dfd8f

  template:
    metadata:
      labels:
        app: user
        project: roboshop
        tier: app
        pod-template-hash: 58f6dfd8f

    spec:
      containers:
        - name: user
          image: vanimina/user:1.0.0
          imagePullPolicy: Always

          envFrom:
            - configMapRef:
                name: user

          resources: {}

      restartPolicy: Always
      terminationGracePeriodSeconds: 30
```

> **Important:** `pod-template-hash: 58f6dfd8f` is generated by the Deployment controller. Don't hard-code this value in a hand-maintained ReplicaSet manifest.

---

## ⚙️ ReplicaSet Configuration

| Configuration       | Value                 |
| ------------------- | --------------------- |
| 🔄 ReplicaSet       | `user-58f6dfd8f`      |
| 📦 Namespace        | `roboshop`            |
| 🔢 Desired Replicas | `1`                   |
| 🟢 Current Pod      | `1`                   |
| 🐳 Image            | `vanimina/user:1.0.0` |
| ⚙️ ConfigMap        | `user`                |
| 🏗️ Project         | `roboshop`            |
| 🏷️ Tier            | `app`                 |
| 👑 Owner            | Deployment `user`     |
| 🔴 GitOps           | Argo CD → Deployment  |

---

## 🏗️ Deployment → ReplicaSet → Pod

The Kubernetes resource hierarchy is:

```text
              🔴 Argo CD
                   │
                   ▼
          🚀 Deployment
              user
                   │
                   │ creates/manages
                   ▼
          🔄 ReplicaSet
          user-58f6dfd8f
                   │
                   │ creates/manages
                   ▼
              🟢 Pod
             user-xxxxx
                   │
                   ▼
            👤 User App
```

The Deployment is the primary resource that you manage.

The ReplicaSet is created automatically by the Deployment controller.

---

## 🎯 Replica Management

The ReplicaSet is configured with:

```yaml
spec:
  replicas: 1
```

This means Kubernetes attempts to keep **one User Pod** running.

```text
Desired State:

🔄 ReplicaSet
      │
      │ replicas: 1
      ▼
   🟢 Pod × 1
```

If the Pod unexpectedly fails:

```text
🟢 Pod
  │
  │ Failure
  ▼
❌ Pod Removed
  │
  ▼
🔄 ReplicaSet detects
missing replica
  │
  ▼
🆕 New Pod Created
```

This is the core responsibility of a ReplicaSet.

---

## 🏷️ Pod Template Labels

The ReplicaSet uses labels to identify the Pods it manages:

```yaml
selector:
  matchLabels:
    app: user
    project: roboshop
    tier: app
    pod-template-hash: 58f6dfd8f
```

The Pod template contains the same labels:

```yaml
labels:
  app: user
  project: roboshop
  tier: app
  pod-template-hash: 58f6dfd8f
```

This allows the ReplicaSet to determine which Pods belong to it.

```text
ReplicaSet Selector
        │
        │ matches
        ▼
   Pod Labels
        │
        ▼
    🟢 User Pod
```

---

## 🔢 What Is `pod-template-hash`?

You will notice:

```yaml
pod-template-hash: 58f6dfd8f
```

This is automatically generated by the Deployment controller.

It helps Kubernetes distinguish between different ReplicaSets created from different versions of a Deployment.

For example:

```text
🚀 Deployment: user
        │
        ├── 🔄 ReplicaSet
        │      user-58f6dfd8f
        │      image: 1.0.0
        │
        └── 🔄 ReplicaSet
               user-xxxxxxxx
               image: 1.1.0
```

During a rolling update, the Deployment can create a **new ReplicaSet** while gradually scaling down the old ReplicaSet.

---

## 🔄 Rolling Update

Your Deployment uses:

```yaml
strategy:
  type: RollingUpdate
```

Therefore, when the image changes:

```text
        🚀 Deployment
             user
              │
       ┌──────┴──────┐
       ▼             ▼
  Old ReplicaSet  New ReplicaSet
  1.0.0           1.1.0
       │             │
       ▼             ▼
    🟢 Pod         🟢 Pod
       │             │
       └──────┬──────┘
              │
              ▼
       Old Pod Removed
```

This provides controlled application updates without immediately terminating all existing Pods.

---

## ⚙️ ConfigMap Integration

The User container loads environment variables from the `user` ConfigMap:

```yaml
envFrom:
  - configMapRef:
      name: user
```

Architecture:

```text
📦 ConfigMap
    user
     │
     │ Environment Variables
     ▼
🔄 ReplicaSet
     │
     ▼
🟢 User Pod
     │
     ▼
👤 User Container
```

---

## 🔗 User Service Integration

The `user` Kubernetes Service selects Pods using:

```yaml
selector:
  app: user
  project: roboshop
  tier: app
```

Therefore:

```text
              🔵 Service
                 user:8080
                    │
                    │ selector
                    ▼
             🟢 User Pod
                    │
                    ▼
              👤 User App
```

The `pod-template-hash` does not need to be included in the Service selector.

This allows the Service to continue routing traffic across different ReplicaSets during rolling updates.

---

## 🔴 Argo CD Relationship

The ReplicaSet contains:

```yaml
argocd.argoproj.io/tracking-id: user:apps/Deployment:roboshop/user
```

Notice that the tracking ID identifies the **Deployment**, not an independently managed ReplicaSet.

This is an important indication that the ReplicaSet is part of the Deployment-managed resource hierarchy.

```text
📦 Git Repository
       │
       ▼
🔴 Argo CD Application
       │
       ▼
🚀 Deployment
       │
       │ Kubernetes Controller
       ▼
🔄 ReplicaSet
       │
       ▼
🟢 Pod
```

---

## 🧪 Useful Commands

### List User ReplicaSets

```bash
kubectl get rs -n roboshop -l app=user
```

### Detailed ReplicaSet Information

```bash
kubectl describe rs user-58f6dfd8f -n roboshop
```

### Check ReplicaSet Pods

```bash
kubectl get pods -n roboshop \
  -l app=user,project=roboshop,tier=app
```

### Check Deployment Ownership

```bash
kubectl get rs user-58f6dfd8f \
  -n roboshop \
  -o jsonpath='{.metadata.ownerReferences[*].name}'
```

Expected:

```text
user
```

### Check ReplicaSets During Rollout

```bash
kubectl get rs -n roboshop -l app=user
```

---

## 🧹 Runtime Fields Removed

The original `kubectl get rs -o yaml` output contains several Kubernetes-generated fields.

These should normally not be copied into a Git-managed manifest:

```yaml
creationTimestamp:
generation:
resourceVersion:
uid:
status:
observedGeneration:
availableReplicas:
fullyLabeledReplicas:
readyReplicas:
terminatingReplicas:
deployment.kubernetes.io/desired-replicas:
deployment.kubernetes.io/max-replicas:
deployment.kubernetes.io/revision:
```

Also avoid manually maintaining:

```yaml
pod-template-hash:
```

because the Deployment controller generates it.

---

## 🏆 Complete User Service Resource Flow

```text
                    🔴 Argo CD
                        │
                        ▼
                🚀 Deployment
                    user
                        │
                        │ creates
                        ▼
                🔄 ReplicaSet
              user-58f6dfd8f
                        │
                        │ maintains
                        ▼
                   🟢 Pod
                        │
                        ▼
                 👤 User App
                        │
             ┌──────────┴──────────┐
             │                     │
             ▼                     ▼
       📦 ConfigMap          💾 MongoDB
          user
             
                    ▲
                    │
                    │ traffic
                    │
              🔵 Service
                 user:8080
```

### 🎯 Key Points

* 🔄 ReplicaSet maintains the desired number of Pods.
* 🚀 The `user` Deployment creates and manages the ReplicaSet.
* 🟢 Current desired replicas: **1**.
* 🐳 User image: `vanimina/user:1.0.0`.
* ⚙️ Configuration comes from ConfigMap `user`.
* 🏷️ `pod-template-hash` identifies the specific Deployment revision.
* 🔵 The `user` Service routes traffic to matching User Pods.
* 🔴 Argo CD primarily manages the Deployment; the ReplicaSet is generated by Kubernetes.
* 🧹 Runtime-generated metadata/status should not be committed to Git.
---------------------------------------------------------------------------------------------------------------------------
# 🟢 User Service – Kubernetes Pod

The **User Service Pod** is the runtime unit that runs the `vanimina/user:1.0.0` container inside the `roboshop` namespace.

This Pod is **automatically created and managed by the User Service ReplicaSet**, which is itself managed by the `user` Deployment.

> ⚠️ **GitOps Note:** Do not normally create or maintain this Pod YAML directly in Git. The Deployment is the desired-state resource; Kubernetes generates the ReplicaSet and Pod.

---

## 📄 Clean Pod Manifest

The following is a simplified, README-friendly representation of the Pod:

```yaml id="g5k7qp"
apiVersion: v1
kind: Pod

metadata:
  name: user
  namespace: roboshop

  labels:
    app: user
    project: roboshop
    tier: app

spec:
  containers:
    - name: user
      image: vanimina/user:1.0.0
      imagePullPolicy: Always

      envFrom:
        - configMapRef:
            name: user

      resources: {}

  restartPolicy: Always
  dnsPolicy: ClusterFirst
  terminationGracePeriodSeconds: 30

  serviceAccountName: default
```

> The actual Pod name, `pod-template-hash`, node assignment, ServiceAccount token volume, Pod IP, container ID, and runtime status are generated by Kubernetes.

---

## ⚙️ Pod Configuration

| Configuration        | Value                       |
| -------------------- | --------------------------- |
| 🟢 Pod               | `user-58f6dfd8f-g5dwf`      |
| 📦 Namespace         | `roboshop`                  |
| 👤 Container         | `user`                      |
| 🐳 Image             | `vanimina/user:1.0.0`       |
| 🔄 Image Pull Policy | `Always`                    |
| ⚙️ ConfigMap         | `user`                      |
| 🔢 Replicas          | `1`                         |
| 🔄 Restart Policy    | `Always`                    |
| 🌐 DNS Policy        | `ClusterFirst`              |
| 🏗️ Project          | `roboshop`                  |
| 🏷️ Tier             | `app`                       |
| 📊 QoS Class         | `BestEffort`                |
| 👑 Owner             | ReplicaSet `user-58f6dfd8f` |

---

## 🏗️ Pod Ownership

This Pod is not independent.

The ownership hierarchy is:

```text id="v0u9v4"
🔴 Argo CD
     │
     ▼
🚀 Deployment
   user
     │
     ▼
🔄 ReplicaSet
user-58f6dfd8f
     │
     ▼
🟢 Pod
user-58f6dfd8f-g5dwf
     │
     ▼
🐳 Container
vanimina/user:1.0.0
```

The original Pod contains an `ownerReferences` entry pointing to:

```yaml id="u9k6lr"
kind: ReplicaSet
name: user-58f6dfd8f
```

This tells Kubernetes that the ReplicaSet owns the Pod.

---

## 🐳 User Container

The Pod runs one container:

```yaml id="m2j1e8"
containers:
  - name: user
    image: vanimina/user:1.0.0
    imagePullPolicy: Always
```

The application image is:

```text
docker.io/vanimina/user:1.0.0
```

The container was running successfully in the captured Pod state.

```text id="x9e2gq"
🟢 Pod
   │
   └── 🐳 user container
           │
           └── vanimina/user:1.0.0
```

---

## ⚙️ ConfigMap Integration

The User container receives environment variables from the `user` ConfigMap:

```yaml id="t6oc4r"
envFrom:
  - configMapRef:
      name: user
```

The configuration flow is:

```text id="z0td5j"
📦 ConfigMap
    user
     │
     │ envFrom
     ▼
🟢 User Pod
     │
     ▼
🐳 User Container
```

For your current configuration, the ConfigMap contains:

```yaml id="xw8xjy"
data:
  MONGO: "true"
```

The application determines the exact behavior of this variable.

---

## 🌐 Kubernetes Networking

The Pod received a Pod IP from the Kubernetes cluster:

```text
10.244.1.8
```

However, applications should **not normally connect directly to this Pod IP** because Pod IPs can change when Pods are recreated.

Instead, applications should communicate through the Kubernetes Service:

```text
user:8080
```

Traffic flow:

```text id="u7e2sa"
Other RoboShop Services
          │
          ▼
   🔵 user Service
       :8080
          │
          ▼
    🟢 User Pod
      :8080
          │
          ▼
     🐳 User App
```

---

## 🔗 Service Selector

The `user` Service uses:

```yaml id="xw6v91"
selector:
  app: user
  project: roboshop
  tier: app
```

The Pod has matching labels:

```yaml id="y2p9az"
labels:
  app: user
  project: roboshop
  tier: app
```

Therefore the Service can discover this Pod as a backend endpoint.

```text id="s6g1so"
🔵 user Service
       │
       │ selector
       ▼
┌─────────────────────┐
│ 🟢 User Pod         │
│                     │
│ app=user             │
│ project=roboshop     │
│ tier=app             │
└─────────────────────┘
```

---

## 🔄 Pod Lifecycle

The Pod lifecycle is controlled by its ReplicaSet:

```text id="p5q3n8"
        🔄 ReplicaSet
             │
             │ desired: 1
             ▼
         🟢 Pod
             │
      ┌──────┴──────┐
      │             │
      ▼             ▼
   Running       Failed
      │             │
      │             ▼
      │        ❌ Pod removed
      │             │
      │             ▼
      │        🆕 New Pod
      │
      ▼
🐳 User Container
```

If this Pod is deleted:

```bash id="a5c4j9"
kubectl delete pod user-58f6dfd8f-g5dwf -n roboshop
```

the ReplicaSet detects that the desired replica is missing and creates a replacement Pod.

---

## 🔄 Pod Restart Policy

The Pod uses:

```yaml id="b0ec2a"
restartPolicy: Always
```

This means Kubernetes will restart the container when appropriate while the Pod is managed by the ReplicaSet.

---

## 🌐 DNS Configuration

The Pod uses:

```yaml id="k7qv8p"
dnsPolicy: ClusterFirst
```

This allows the Pod to use Kubernetes cluster DNS for service discovery.

For example, the User Service can be reached internally using:

```text
user.roboshop.svc.cluster.local
```

or, from the same namespace:

```text
user
```

---

## 🔐 Service Account

The Pod uses the default ServiceAccount:

```yaml id="n7k0az"
serviceAccountName: default
```

Kubernetes automatically provides the Pod with the projected ServiceAccount-related volume:

```text
/var/run/secrets/kubernetes.io/serviceaccount
```

The original Pod output shows Kubernetes-generated resources such as:

```text
kube-api-access-vrq9c
```

These should generally **not be manually added** to your application manifest.

---

## 📊 Pod Status

At the time of your captured output, the Pod was:

```text
Phase:           Running
Ready:           True
ContainersReady: True
Initialized:     True
PodScheduled:    True
Restart Count:   0
```

This indicates that the User container was running successfully at that point.

> Pod status is runtime information and should not be stored in the Git-managed manifest.

---

## ⚠️ BestEffort QoS

Your Pod currently has:

```yaml
resources: {}
```

Therefore Kubernetes assigned it the:

```text
QoS Class: BestEffort
```

For production workloads, it is generally better to define resource requests and limits.

Example:

```yaml id="0fr5ea"
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "512Mi"
```

This is especially important because your **User HPA uses CPU utilization**.

The HPA calculates CPU utilization relative to CPU requests, so defining a CPU request is important for predictable CPU-based autoscaling.

---

## 🧪 Useful Kubernetes Commands

### Check User Pod

```bash id="0pk2pn"
kubectl get pods -n roboshop -l app=user
```

### Detailed Pod Information

```bash id="1xq4wd"
kubectl describe pod user-58f6dfd8f-g5dwf -n roboshop
```

### View Pod Logs

```bash id="x1d4u8"
kubectl logs user-58f6dfd8f-g5dwf -n roboshop
```

### Follow Pod Logs

```bash id="g1t8v2"
kubectl logs -f user-58f6dfd8f-g5dwf -n roboshop
```

### Execute a Command in the Container

```bash id="q8z2vn"
kubectl exec -it user-58f6dfd8f-g5dwf -n roboshop -- sh
```

### Check Pod IP

```bash id="m4y7sa"
kubectl get pod user-58f6dfd8f-g5dwf \
  -n roboshop \
  -o wide
```

### Check Pod Labels

```bash id="j2x8qk"
kubectl get pod user-58f6dfd8f-g5dwf \
  -n roboshop \
  --show-labels
```

### Check Pod YAML

```bash id="r8k4wf"
kubectl get pod user-58f6dfd8f-g5dwf \
  -n roboshop \
  -o yaml
```

---

## 🧹 Runtime Fields Removed

Your original Pod output contains many fields that are generated dynamically by Kubernetes.

These should **not normally be copied into your GitOps manifest**:

```yaml id="z2p5nr"
creationTimestamp:
generateName:
generation:
resourceVersion:
uid:
ownerReferences:
status:
containerStatuses:
containerID:
imageID:
hostIP:
hostIPs:
podIP:
podIPs:
startTime:
phase:
qosClass:
```

The following are also generated by Kubernetes:

```text
kube-api-access-vrq9c
service account token
Pod IP
Node assignment
Container ID
Image digest
Pod status
Pod readiness conditions
```

---

## 🔴 GitOps Principle

The Pod represents the **runtime state**, while your Deployment represents the **desired state**.

```text
                 📦 Git
                   │
                   ▼
              🔴 Argo CD
                   │
                   ▼
            🚀 Deployment
                   │
                   ▼
             🔄 ReplicaSet
                   │
                   ▼
              🟢 Pod
                   │
                   ▼
             🐳 Container
```

You should normally manage:

```text
✅ Deployment
✅ Service
✅ ConfigMap
✅ HPA
```

rather than manually managing:

```text
❌ Generated ReplicaSet
❌ Generated Pod
```

---

## 🏆 Complete User Service Architecture

```text
                         🔴 Argo CD
                             │
                             ▼
                    🚀 Deployment
                        user
                             │
                             ▼
                    🔄 ReplicaSet
                   user-58f6dfd8f
                             │
                             ▼
                       🟢 User Pod
                             │
                       ┌─────┴─────┐
                       │           │
                       ▼           ▼
                ⚙️ ConfigMap   🐳 Container
                    user       user:1.0.0
                                   │
                                   ▼
                              👤 User App
                                   │
                                   ▼
                              💾 MongoDB

                🔵 user Service :8080
                         │
                         ▼
                     User Pod
```

### 🎯 Key Points

* 🟢 Pod runs the actual User Service container.
* 🔄 ReplicaSet maintains the desired Pod count.
* 🚀 Deployment manages the ReplicaSet.
* 🔵 Service provides stable networking to the Pod.
* ⚙️ ConfigMap provides application configuration.
* 📈 HPA can scale the Deployment based on CPU metrics.
* 🔴 Argo CD manages the desired state through GitOps.
* 🌐 Pod IPs are ephemeral; use the Kubernetes Service for application communication.
* 🧹 Pod runtime metadata and status should not be committed to Git.
--------------------------------------------------------------------------------------------------------------------------
