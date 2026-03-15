# Deploy Sealed Secrets with Helm

This guide explains how to deploy and use Sealed Secrets with Helm.

## 1. Install Kubeseal CLI

The `kubeseal` command-line tool is used to encrypt secrets.

```bash
# Download the latest release from https://github.com/bitnami-labs/sealed-secrets/releases
wget https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.24.4/kubeseal-0.24.4-linux-amd64.tar.gz
tar -xvzf kubeseal-0.24.4-linux-amd64.tar.gz
sudo install -m 755 kubeseal /usr/local/bin/kubeseal
```

## 2. Install Sealed Secrets Controller with Helm

The Sealed Secrets controller is responsible for decrypting secrets in the cluster.

```bash
helm repo add sealed-secrets https://bitnami-labs.github.io/sealed-secrets
helm repo update
helm install sealed-secrets sealed-secrets/sealed-secrets -n kube-system
```

## 3. Fetch Public Key from Controller

Fetch the public key from the Sealed Secrets controller. This key is used to encrypt your secrets.

```bash
kubeseal --fetch-cert \
--controller-name sealed-secrets \
--controller-namespace kube-system > pub-sealed-secrets.pem
```
**Note:** The `--controller-name` is the name of the sealed-secrets-controller service. If you installed the chart with a different release name, the service name will be different. For example if you used `helm install my-release sealed-secrets/sealed-secrets`, the controller name would be `my-release-sealed-secrets`.

## 4. Prepare Secret YAML File

Create a standard Kubernetes Secret YAML file.

**secret.yaml:**
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
  namespace: default
data:
  password: <your-base64-encoded-password>
```

To get the base64 encoded password, you can use the following command:
```bash
echo -n "my-password" | base64
```

## 5. Encrypt Secret with Public Key

Encrypt the secret file using `kubeseal` and the public key.

```bash
kubeseal --cert pub-sealed-secrets.pem < secret.yaml > sealed-secret.yaml
```

The resulting `sealed-secret.yaml` can be safely committed to your Git repository.
