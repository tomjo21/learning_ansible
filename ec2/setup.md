# Setup EC2 Collection and Authentication

## Install Boto3

Install the AWS SDK for Python (`boto3`), which is required by Ansible AWS modules.

```bash
pip install boto3
```

## Install the Amazon AWS Collection

Install the official Ansible AWS collection.

```bash
ansible-galaxy collection install amazon.aws
```

## Set Up Ansible Vault

### 1. Create a Vault Password File

Generate a password file that will be used to encrypt and decrypt your vault.

```bash
openssl rand -base64 2048 > vault.pass
```

### 2. Create an Encrypted Vault File

Store your AWS credentials in an encrypted file using Ansible Vault.

```bash
ansible-vault create group_vars/all/pass.yml --vault-password-file vault.pass
```

### 3. Add Your AWS Credentials

Inside `group_vars/all/pass.yml`, add the following variables:

```yaml
ec2_access_key: YOUR_AWS_ACCESS_KEY
ec2_secret_key: YOUR_AWS_SECRET_KEY
```

These variables can then be referenced in your playbooks or roles:

```yaml
aws_access_key: "{{ ec2_access_key }}"
aws_secret_key: "{{ ec2_secret_key }}"
```