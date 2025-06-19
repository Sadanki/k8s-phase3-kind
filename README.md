 **Phase 3** Kubernetes learning project:

---

```markdown
# 🚀 Kubernetes Phase 3: StatefulSets, Jobs, CronJobs & Custom Images with KIND

Welcome to **Phase 3** of the Kubernetes learning series!  
This project demonstrates advanced Kubernetes workloads such as:

- 🔁 StatefulSets with stable network identity & persistent volumes
- 🛠️ One-time Jobs
- 🕒 Scheduled CronJobs
- 📦 Custom Docker image deployment using KIND (Kubernetes IN Docker)

---

## 📁 Project Structure

```

k8s-phase3-kind/
├── custom-nginx/
│   ├── Dockerfile              # Custom Nginx Dockerfile
│   └── index.html              # Custom HTML file served by Nginx
├── cronjob.yaml                # Scheduled job example
├── headless-service.yaml       # Required for StatefulSet DNS
├── job.yaml                    # One-time job
├── pvc.yaml                    # (Optional) Predefined PVC
├── service.yaml                # Service for StatefulSet
├── statefulset.yaml            # StatefulSet with volume claims
└── README.md                   # This documentation

````

---

## 📦 Step-by-Step Guide

### 1️⃣ Create KIND Cluster

```bash
kind create cluster --name k8s-phase-demo
````

### 2️⃣ Build and Load Custom Docker Image into KIND

```bash
cd custom-nginx/
docker build -t custom-nginx:v3 .
kind load docker-image custom-nginx:v3 --name k8s-phase-demo
```

> This builds a custom Nginx server using your own `index.html`.

---

### 3️⃣ Apply Kubernetes Manifests

```bash
kubectl apply -f headless-service.yaml
kubectl apply -f statefulset.yaml
kubectl apply -f service.yaml
kubectl apply -f job.yaml
kubectl apply -f cronjob.yaml
```

---

## 🔍 Verify Resources

```bash
kubectl get pods
kubectl get pvc
kubectl get svc
kubectl get statefulset
kubectl get jobs
kubectl get cronjobs
```

---

## 🌐 Access the StatefulSet Application

Port forward the first pod:

```bash
kubectl port-forward pod/web-0 8080:80
```

Then open your browser at: [http://localhost:8080](http://localhost:8080)

---

## ♻️ Update Custom Image

After modifying `index.html`, rebuild and reload the image:

```bash
cd custom-nginx/
docker build -t custom-nginx:v3 .
kind load docker-image custom-nginx:v3 --name k8s-phase-demo
kubectl rollout restart statefulset web
```

---

## 📘 Key Concepts Covered

| Component                 | Purpose                                    |
| ------------------------- | ------------------------------------------ |
| **StatefulSet**           | Ensures unique identity and stable storage |
| **Headless Service**      | Enables direct pod DNS (e.g., `web-0`)     |
| **PersistentVolumeClaim** | Gives each pod its own storage             |
| **Job**                   | Executes a task once, then completes       |
| **CronJob**               | Runs a task on a schedule                  |
| **Custom Image**          | Docker image with HTML served via Nginx    |

---

## 📚 Next Steps

Phase 4 (future scope) may include:

* Ingress controllers
* Helm chart templating
* Secrets & ConfigMaps
* Horizontal Pod Autoscaler

---

> ✍️ Maintained as part of a Kubernetes hands-on series.

```

