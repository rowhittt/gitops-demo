# GitOps-Based Kubernetes Deployment (App-of-Apps Pattern)

This project demonstrates a hierarchical "App-of-Apps" architecture using ArgoCD and Minikube to manage multi-tenant Kubernetes workloads. The deployment lifecycle is fully automated, leveraging GitOps principles to ensure a Zero-Touch pipeline and enforce Self-Healing policies.

## Project Overview

- **Architecture**: App-of-Apps pattern with ArgoCD.
- **Cluster**: Minikube for local Kubernetes cluster.
- **Automation**: Zero-Touch pipeline triggered by Git commits.
- **Consistency**: Self-Healing policies to prevent configuration drift.

---

## Prerequisites

1. **Install Minikube**:
   ```bash
   brew install minikube
   ```

2. **Install kubectl**:
   ```bash
   brew install kubectl
   ```

3. **Install ArgoCD CLI**:
   ```bash
   brew install argocd
   ```

4. **Start Minikube**:
   ```bash
   minikube start
   ```

---

## Setup Instructions

### 1. Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2. Access ArgoCD UI

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

- Open your browser and navigate to `https://localhost:8080`.
- Login using the default credentials:
  - **Username**: `admin`
  - **Password**: Run the following command to retrieve the password:
    ```bash
    kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
    ```

### 3. Deploy the Root Application

```bash
kubectl apply -f root-app.yaml
```

### 4. Verify Applications

```bash
kubectl get applications -n argocd
```

---

## Repository Structure

```
gitops-demo/
├── apps/
│   └── nginx-app-definition.yaml   <-- Tells ArgoCD how to deploy the app
├── manifests/
│   └── deployment.yaml             <-- The actual K8s Nginx code
└── root-app.yaml                   <-- The "Boss" that watches the /apps folder
```

---

## Key Features

1. **App-of-Apps Pattern**:
   - The `root-app.yaml` acts as the parent application, managing child applications in the `/apps` folder.

2. **Zero-Touch Pipeline**:
   - All infrastructure changes are triggered by Git commits.

3. **Self-Healing Policies**:
   - ArgoCD automatically detects and corrects configuration drift.

---

## Commands Reference

### Sync Applications
```bash
argocd app sync root-app
```

### Check Application Status
```bash
argocd app get root-app
```

### View Logs
```bash
kubectl logs -n argocd deployment/argocd-application-controller
```

---

## Screenshots

### ArgoCD Application View
![ArgoCD Application View](attachments/screenshot-argocd-app.png)
![ArgoCD Application View](https://github.com/rowhittt/gitops-demo/blob/main/screenshots/argocd-app.png)

### Nginx Application View
![Nginx Application View](attachments/screenshot-nginx-app.png)
![Nginx Application View](https://github.com/rowhittt/gitops-demo/blob/main/screenshots/nginx-app.png)

---

## Troubleshooting

### Error: `app path does not exist`
- Ensure the `path` in `nginx-app-definition.yaml` and `root-app.yaml` matches the repository structure.
- Verify the repository is up-to-date:
  ```bash
  git pull origin main
  ```

### Error: `argocd-application-controller not found`
- Ensure ArgoCD is installed and running:
  ```bash
  kubectl get pods -n argocd
  ```

---

## Resources

- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)
