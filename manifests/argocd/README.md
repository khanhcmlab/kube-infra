# Install ArgoCD with Helm

This guide explains how to install ArgoCD with Helm.

## 1. Add Argo Project Helm repository

```bash
helm repo add argo https://argoproj.github.io/argo-helm
```

## 2. Update Helm repositories

```bash
helm repo update
```

## 3. Install ArgoCD

```bash
helm install argocd argo/argo-cd --namespace argocd --create-namespace
```

## 4. Access the ArgoCD API Server

By default, the ArgoCD API server is not exposed with an external IP. To access it, use one of the following methods:

### Port Forwarding

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
You can now access the ArgoCD UI at `https://localhost:8080`.

### Load Balancer

If you have a load balancer available, you can change the service type to `LoadBalancer`:

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

## 5. Login to ArgoCD

The initial password for the `admin` user is auto-generated and stored in a Kubernetes secret.

### Get the initial password

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

You can now login to the ArgoCD UI with the username `admin` and the password you just retrieved.
