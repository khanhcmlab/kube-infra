# Install ArgoCD with Helm

This guide explains how to install ArgoCD with Helm using the official ArgoCD Helm chart.

## 1. Add ArgoCD Helm repository

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
```

## 2. Install ArgoCD

You can install ArgoCD in the `argocd` namespace. The `--create-namespace` flag will automatically create the namespace if it doesn't exist.

```bash
helm install argocd argo/argo-cd -n argocd --create-namespace
```

If you wish to pass custom configuration (such as High Availability options), use a `values.yaml` file:

```bash
helm install argocd argo/argo-cd -n argocd --create-namespace -f values.yaml
```

## 3. Create ExternalName service for argocd-server

Create an ExternalName service to expose the `argocd-server` to other namespaces.

**argocd-server-external.yaml:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: argocd-server
  namespace: default
spec:
  type: ExternalName
  externalName: argocd-server.argocd.svc.cluster.local
  ports:
  - port: 80
    targetPort: 80
  - port: 443
    targetPort: 443
```

Apply the yaml file:
```bash
kubectl apply -f argocd-server-external.yaml
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
