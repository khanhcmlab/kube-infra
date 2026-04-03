# kube-vip DaemonSet Deployment

## Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to communicate with your cluster
- `jq` installed on your workstation
- `ctr` (containerd CLI) available on the node where you run the script, **or** Docker (uncomment the Docker alias in `vip.sh`)

## Steps

### 1. Generate the DaemonSet manifest

Edit `vip.sh` to set the variables that match your environment:

| Variable    | Description                                      | Default           |
|-------------|--------------------------------------------------|-------------------|
| `VIP`       | Virtual IP address to advertise                  | `192.168.1.100`   |
| `INTERFACE` | Network interface used for ARP announcements     | `eth0`            |
| `KVVERSION` | kube-vip image tag (auto-detected from GitHub)   | latest release    |

Then run the script and capture its output as a manifest file:

```bash
chmod +x manifests/vip/vip.sh
bash manifests/vip/vip.sh > kube-vip-daemonset.yaml
```

### 2. Apply the DaemonSet

```bash
kubectl apply -f kube-vip-daemonset.yaml
```

Verify the DaemonSet is running:

```bash
kubectl get daemonset -n kube-system kube-vip-ds
```
