**Kubernetes Phase 3: StatefulSets, Jobs, and CronJobs** — including **what each component is**, **what  implemented**, and **which commands used**.

---

## 🚀 **Kubernetes Phase 3: Summary & Explanation**

### ✅ **Goals of Phase 3**

In this phase, you learned to work with:

| Component                     | Purpose                                                           |
| ----------------------------- | ----------------------------------------------------------------- |
| `StatefulSet`                 | Deploy stateful apps where each pod has stable identity & storage |
| `Job`                         | Run a one-time batch task                                         |
| `CronJob`                     | Run a task on a schedule (like a cron job in Linux)               |
| `PersistentVolumeClaim (PVC)` | Give pods durable storage (used with StatefulSets)                |
| `Custom Docker Image`         | Build your own Nginx image with a custom `index.html`             |

---

## 📦 **1. Built a Custom Nginx Image**

### **Files Created:**

* `Dockerfile`
* `index.html`

### **Dockerfile Example:**

```Dockerfile
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html
```

### **Commands Used:**

```bash
cd custom-nginx/
docker build -t custom-nginx:v3 .
kind load docker-image custom-nginx:v3 --name k8s-phase-demo
```

This created a Docker image that Nginx will serve with your custom HTML.

---

## 🧱 **2. Created a StatefulSet**

A **StatefulSet** manages pods with:

* Stable network IDs (`web-0`, `web-1`, `web-2`)
* Individual PVCs (`www-web-0`, etc.)
* Persistent data across restarts

### **Files Used:**

* `statefulset.yaml`
* `headless-service.yaml` (required by StatefulSet)

### **Key YAML Snippet:**

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "web"
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: nginx
        image: custom-nginx:v3
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 1Gi
```

### **Commands Used:**

```bash
kubectl apply -f headless-service.yaml
kubectl apply -f statefulset.yaml
kubectl get pods -w
kubectl get pvc
kubectl exec -it web-0 -- cat /usr/share/nginx/html/index.html
```

You verified each pod has its own HTML copy and PVC:

```bash
kubectl get pvc
kubectl describe pod web-0
```

---

## ⏳ **3. Created a One-Time Job**

A `Job` runs a command **once and exits**.

### **job.yaml**

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: hello-job
spec:
  template:
    spec:
      containers:
      - name: hello
        image: busybox
        command: ["echo", "👋 Hello from the Job"]
      restartPolicy: Never
  backoffLimit: 2
```

### **Command Used:**

```bash
kubectl apply -f job.yaml
kubectl get pods -w  # wait for completion
kubectl logs job/hello-job
```

---

## ⏰ **4. Created a Scheduled CronJob**

A `CronJob` runs **repeatedly on a schedule**, just like Linux cron.

### **cronjob.yaml**

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello-cron
spec:
  schedule: "*/1 * * * *"  # Every minute
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            command: ["echo", "⏰ Hello from the CronJob!"]
          restartPolicy: OnFailure
```

### **Command Used:**

```bash
kubectl apply -f cronjob.yaml
kubectl get cronjobs
kubectl get jobs --watch
kubectl logs job/<job-name>  # Get logs from latest run
```

---

## 🌐 **5. Verified Web App via Port Forwarding**

### **Command:**

```bash
kubectl port-forward pod/web-0 8080:80
```

Then access:

```
http://localhost:8080
```

---

## 🔁 **Rebuild Flow (When Updating index.html)**

1. Update `index.html`
2. Rebuild the image:

   ```bash
   docker build -t custom-nginx:v3 .
   kind load docker-image custom-nginx:v3 --name k8s-phase-demo
   kubectl rollout restart statefulset web
   ```
3. Confirm updated content:

   ```bash
   kubectl exec -it web-0 -- cat /usr/share/nginx/html/index.html
   ```

---

## ✅ **Phase 3: Key Takeaways**

| Topic            | Concept                                      |
| ---------------- | -------------------------------------------- |
| StatefulSet      | Stable pod names + stable storage (PVCs)     |
| Job              | One-time task                                |
| CronJob          | Recurring scheduled task                     |
| PVC              | Each pod in StatefulSet gets its own volume  |
| Headless Service | Required by StatefulSet to manage stable DNS |
| Port-forward     | Lets you test app running inside Kubernetes  |

---


