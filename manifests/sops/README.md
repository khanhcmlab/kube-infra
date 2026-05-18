

Bash

```bash
aws kms create-key --description "SOPS encryption key"
```

_Note the `"Arn"` from the output (e.g., `arn:aws:kms:us-east-1:123456789012:key/abcd-1234...`)._

**(Optional) Create an alias for easier management:**

Bash

```bash
aws kms create-alias --alias-name alias/sops-key --target-key-id <YOUR_KEY_ID>
```

## Step 3: Configure SOPS (`.sops.yaml`)

Create a `.sops.yaml` file at the root of your repository to automatically tell SOPS which KMS key to use for specific files:

YAML

```
creation_rules:
  # Apply this KMS key to any file ending in .enc.yaml
  - path_regex: .*\.enc\.yaml$
    kms: 'arn:aws:kms:us-east-1:123456789012:key/abcd-1234-efgh-5678'
```

_(Replace the ARN with your actual KMS Key ARN from Step 2)_

## Step 4: Encrypt Secrets

Create a plaintext secrets file (e.g., `my-secrets.enc.yaml`) and encrypt it in place:

Bash

```bash
sops --encrypt --in-place my-secrets.enc.yaml
```

_SOPS encrypts the values but leaves the keys in plaintext, allowing you to see the structure. It also appends KMS metadata to the end of the file._

## Step 5: Edit Secrets on the Fly

To safely edit your encrypted file without manually decrypting it first:

Bash

```bash
sops my-secrets.enc.yaml
```

_This opens the decrypted file in your default terminal editor (like Vim or Nano). It automatically re-encrypts the file upon save and exit._ _(Tip: Export your favorite editor using `export EDITOR="code --wait"` for VS Code)_

## Step 6: Decrypt Files

To output the plaintext secrets (e.g., for application injection or CI/CD pipelines):

Bash

```
# Print to stdout
sops --decrypt my-secrets.enc.yaml

# Output to a plaintext file (ensure this file is in your .gitignore!)
sops --decrypt my-secrets.enc.yaml > decrypted-secrets.yaml
```

---

**Note for CI/CD and Production:** 

Bash

```
aws kms create-key --description "SOPS encryption key"
```

_Note the `"Arn"` from the output (e.g., `arn:aws:kms:us-east-1:123456789012:key/abcd-1234...`)._

**(Optional) Create an alias for easier management:**

Bash

```
aws kms create-alias --alias-name alias/sops-key --target-key-id <YOUR_KEY_ID>
```

## Step 3: Configure SOPS (`.sops.yaml`)

Create a `.sops.yaml` file at the root of your repository to automatically tell SOPS which KMS key to use for specific files:

YAML

```
creation_rules:
  # Apply this KMS key to any file ending in .enc.yaml
  - path_regex: .*\.enc\.yaml$
    kms: 'arn:aws:kms:us-east-1:123456789012:key/abcd-1234-efgh-5678'
```

_(Replace the ARN with your actual KMS Key ARN from Step 2)_

## Step 4: Encrypt Secrets

Create a plaintext secrets file (e.g., `my-secrets.enc.yaml`) and encrypt it in place:

Bash

```
sops --encrypt --in-place my-secrets.enc.yaml
```

_SOPS encrypts the values but leaves the keys in plaintext, allowing you to see the structure. It also appends KMS metadata to the end of the file._

## Step 5: Edit Secrets on the Fly

To safely edit your encrypted file without manually decrypting it first:

Bash

```
sops my-secrets.enc.yaml
```

_This opens the decrypted file in your default terminal editor (like Vim or Nano). It automatically re-encrypts the file upon save and exit._ _(Tip: Export your favorite editor using `export EDITOR="code --wait"` for VS Code)_

## Step 6: Decrypt Files

To output the plaintext secrets (e.g., for application injection or CI/CD pipelines):

Bash

```
# Print to stdout
sops --decrypt my-secrets.enc.yaml

# Output to a plaintext file (ensure this file is in your .gitignore!)
sops --decrypt my-secrets.enc.yaml > decrypted-secrets.yaml
```

---

**Note for CI/CD and Production:** Ensure your deployment environment (e.g., GitHub Actions, AWS EC2, EKS) assumes an AWS IAM Role with explicit `kms:Decrypt` permissions for your KMS Key ARN.

