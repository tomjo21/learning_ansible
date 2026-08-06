# Ansible Vault

Ansible Vault is a feature that allows you to securely encrypt sensitive information such as passwords, API keys, SSH keys, and other confidential data used in Ansible playbooks and inventories.

By encrypting sensitive data, Ansible Vault helps protect credentials while allowing them to be safely stored in version control systems.

---

## Why Use Ansible Vault?

- Securely store passwords and secrets.
- Encrypt configuration files containing sensitive data.
- Prevent credentials from being exposed in Git repositories.
- Integrate securely with playbooks and roles.

---

## Create a Vault Password File

Generate a password file that will be used to encrypt and decrypt vault files.

```bash
openssl rand -base64 2048 > vault.pass
```

---

## Create a New Vault

Create an encrypted vault file.

```bash
ansible-vault create group_vars/all/pass.yml --vault-password-file vault.pass
```

Example:

```yaml
ec2_access_key: YOUR_AWS_ACCESS_KEY
ec2_secret_key: YOUR_AWS_SECRET_KEY
```

---

## View an Encrypted Vault

```bash
ansible-vault view group_vars/all/pass.yml --vault-password-file vault.pass
```

---

## Edit an Encrypted Vault

```bash
ansible-vault edit group_vars/all/pass.yml --vault-password-file vault.pass
```

---

## Encrypt an Existing File

```bash
ansible-vault encrypt secrets.yml --vault-password-file vault.pass
```

---

## Decrypt a Vault File

```bash
ansible-vault decrypt secrets.yml --vault-password-file vault.pass
```

---

## Change the Vault Password

```bash
ansible-vault rekey secrets.yml --vault-password-file vault.pass
```

---

## Use Vault Variables in a Playbook

Reference vault variables using Jinja2 syntax.

```yaml
aws_access_key: "{{ ec2_access_key }}"
aws_secret_key: "{{ ec2_secret_key }}"
```

Run the playbook with the vault password file.

```bash
ansible-playbook playbook.yml --vault-password-file vault.pass
```

---

## Encrypted Vault Example

After encryption, the vault file will look similar to:

```text
$ANSIBLE_VAULT;1.1;AES256
613732333937663566353636393231633932...
663833366436643864393437326636346266...
```

The contents are encrypted and cannot be read without the correct vault password.

---

## Common Ansible Vault Commands

| Command | Description |
|---------|-------------|
| `ansible-vault create <file>` | Create a new encrypted vault file. |
| `ansible-vault view <file>` | View an encrypted vault file. |
| `ansible-vault edit <file>` | Edit an encrypted vault file. |
| `ansible-vault encrypt <file>` | Encrypt an existing file. |
| `ansible-vault decrypt <file>` | Decrypt an encrypted file. |
| `ansible-vault rekey <file>` | Change the encryption password of a vault file. |

---

## Best Practices

- Never commit the `vault.pass` file to a Git repository.
- Store vault passwords securely.
- Encrypt only sensitive information.
- Use `group_vars` or `host_vars` to organize encrypted variables.
- Use Ansible Vault whenever credentials or secrets are required in automation.