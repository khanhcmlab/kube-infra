Create secret for Cloudflare tunnel

```
kubectl create secret generic tunnel-token --from-literal=token=<tunnel token>
```

Apply deployment for Cloudflare tunnel

```
kubectl apply -f deployment.yaml
```
