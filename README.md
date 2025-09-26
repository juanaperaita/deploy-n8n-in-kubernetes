# 🚀 Deploy n8n in Kubernetes cluster

This repo has all the files to be able to deploy n8n in a Kubernetes cluster locally.

## Previous requirements

- Minikube, K3s or any Kubernetes cluster.
- `kubectl` correctly set.

If you wish to see if everything is ready before deployment, you can **verify the cluster**:
```bash
kubectl cluster-info
kubectl get nodes
```

## Deploy


### 1. 🔧 Apply YAML files in correct order

Create namespace:
```shell
kubectl create namespace n8n
```

Create services:
```shell
kubectl apply -f n8n-service.yaml
kubectl apply -f n8n-service-postgres.yaml
```

Crete persistent volumes (PVC):
```shell
kubectl apply -f n8n-pvc.yaml
kubectl apply -f n8n-pvc-postgres.yaml
```

Create ingress:
```shell
kubectl apply -f n8n-ingress.yaml
```

Deploy PostgreSQL:
```shell
kubectl apply -f n8n-deployment-postgres.yaml
```

Create database:
```shell
psql -d postgres -U postgres
CREATE DATABASE n8n;
CREATE USER n8n WITH PASSWORD 'n8neconomia';
ALTER DATABASE n8n OWNER TO n8n;
GRANT ALL PRIVILEGES ON DATABASE n8n TO n8n;
\q
psql -d n8n -U n8n -W
```

Deploy n8n:
```shell
kubectl apply -f n8n-deployment.yaml
```

### 2. 🔎 Verify that pods are working correctly

Verify pods (this might take some time):
```shell
kubectl get pods -n n8n
```

Verify services:
```shell
kubectl get svc -n n8n
```

### 3. 🗃️ Configure PostgreSQL database

Connect to PostgreSQL with correct credentials
```shell
kubectl exec -it postgres-statefulset-0 -n n8n -- psql -d n8n -U n8n
```

Inside psql, the database is already created, so we just need to check that it exists:
```shell
\l
```

Then exit psql:
```shell
\q
```

### 4. ✨ Access n8n application

Use [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) to access n8n:
```shell
kubectl port-forward n8n-deployment-59ffb44cc7-smbtp -n n8n 5678:5678
```

Access from browser:
```shell
http://localhost:5678
```

### 5. 📥 Create data backup

PostgreSQL backup:
```shell
kubectl exec postgres-statefulset-0 -n n8n -- pg_dump -U n8n n8n > backup-n8n.sql
```

Restore backup:
```shell
kubectl exec -i postgres-statefulset-0 -n n8n -- psql -U n8n n8n < backup-n8n.sql
```

### (Optional) Access database

To access database from pgAdmin, use port-forward:
```shell
kubectl port-forward postgres-statefulset-0 -n n8n 5433:5432
```

Then connect to your db server from pgAdmin UI using your db creds.