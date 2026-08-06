# Advanced Ansible Task Execution

This document covers advanced Ansible features that improve task execution, modularity, error handling, and deployment strategies.

---

# 1. Serial

## Definition

`serial` controls how many hosts Ansible processes at a time instead of executing tasks on all hosts simultaneously.

It is commonly used for **rolling deployments** to avoid downtime.

---

## Syntax

```yaml
- hosts: webservers
  serial: 2
```

or

```yaml
serial: 50%
```

---

## Example

Inventory

```
web1
web2
web3
web4
web5
```

Playbook

```yaml
- hosts: webservers
  serial: 2

  tasks:
    - name: Restart Nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
```

Execution

```
Batch 1
web1
web2

↓

Batch 2
web3
web4

↓

Batch 3
web5
```

---

## Use Cases

- Rolling deployments
- Zero downtime deployments
- Large production environments

---

## Interview Points

- Default: All hosts
- `serial: 1` → One host at a time
- `serial: 5` → Five hosts at a time
- `serial: 50%` → Half the hosts at a time

---

# 2. include_tasks vs import_tasks

Both split large playbooks into smaller task files.

## import_tasks

- Static import
- Processed before execution
- Faster
- Does not support loops

```yaml
tasks:
  - import_tasks: install.yml
```

---

## include_tasks

- Dynamic import
- Processed during execution
- Supports loops and conditions

```yaml
tasks:
  - include_tasks: install.yml
```

---

## Dynamic Example

```yaml
- include_tasks: "{{ ansible_os_family }}.yml"
```

Loads different task files based on the operating system.

---

## Comparison

| import_tasks | include_tasks |
|---------------|---------------|
| Static | Dynamic |
| Parse Time | Run Time |
| No Loops | Supports Loops |
| Faster | Flexible |

---

## Interview Points

- Import = Copy-Paste before execution.
- Include = Load when required during execution.

---

# 3. changed_when

## Definition

Overrides Ansible's default changed status.

Useful when a task does not actually modify the system.

---

## Syntax

```yaml
- name: Check OS
  command: cat /etc/os-release
  changed_when: false
```

---

## Example

```yaml
- command: curl localhost:8080
  changed_when: false
```

The task is reported as **OK** instead of **CHANGED**.

---

## Use Cases

- Health checks
- Status commands
- Read-only operations

---

# 4. failed_when

## Definition

Overrides Ansible's default failure condition.

Usually used together with `register`.

---

## Syntax

```yaml
- command: ./health_check.sh
  register: result

  failed_when: "'ERROR' in result.stdout"
```

---

## Example

Even if the command exits successfully, Ansible marks the task as failed when:

```
ERROR
```

is found in the output.

---

## Use Cases

- Custom validation
- Health checks
- Log inspection

---

# changed_when vs failed_when

| changed_when | failed_when |
|---------------|-------------|
| Controls Changed Status | Controls Failure Status |
| Does not stop playbook | May stop playbook |

---

# 5. package Module

## Definition

The generic package manager module.

Instead of using OS-specific modules like:

- apt
- yum
- dnf

use:

```yaml
package:
```

---

## Syntax

```yaml
- name: Install Nginx
  ansible.builtin.package:
    name: nginx
    state: present
```

---

## Benefits

- Cross-platform
- Cleaner playbooks
- No OS-specific package managers

---

# 6. include_role vs import_role

Used to include roles inside playbooks.

---

## import_role

Static loading.

```yaml
tasks:
  - import_role:
      name: nginx
```

---

## include_role

Dynamic loading.

```yaml
tasks:
  - include_role:
      name: nginx
```

---

## Comparison

| import_role | include_role |
|--------------|--------------|
| Static | Dynamic |
| Parse Time | Run Time |
| Faster | More Flexible |

---

# 7. setup Module

## Definition

The `setup` module gathers facts from managed hosts.

Facts include:

- Hostname
- OS
- Memory
- CPU
- Network Interfaces
- IP Addresses

---

## Syntax

```yaml
- setup:
```

CLI

```bash
ansible all -m setup
```

---

## Common Facts

```
ansible_hostname

ansible_distribution

ansible_os_family

ansible_default_ipv4

ansible_processor

ansible_memory_mb
```

---

## Use Cases

- Conditional execution
- Dynamic configuration
- OS-specific tasks

---

# 8. Check Mode

## Definition

Performs a **dry run** without making any changes.

---

## Command

```bash
ansible-playbook playbook.yml --check
```

---

## Benefits

- Validate playbooks
- Preview execution
- Safe production testing

---

# 9. Diff Mode

## Definition

Displays differences between the current and expected configuration.

---

## Command

```bash
ansible-playbook playbook.yml --diff
```

---

## Example

```
- old configuration

+ new configuration
```

---

## Benefits

- Review configuration changes
- Verify template updates
- Useful before deployment

---

# 10. Async & Poll

## Definition

Runs long-running tasks asynchronously.

Useful for operations that take several minutes.

---

## Syntax

Run in background.

```yaml
- name: Database Backup
  command: backup.sh
  async: 600
  poll: 0
```

---

Wait for completion.

```yaml
- name: Upgrade System
  command: upgrade.sh
  async: 1800
  poll: 10
```

Meaning:

- Maximum runtime: **1800 seconds**
- Check status every **10 seconds**

---

## Use Cases

- Database backups
- System upgrades
- Large downloads
- Software installations

---

# Interview Cheat Sheet

| Feature | Purpose |
|----------|----------|
| serial | Rolling deployment (batch execution) |
| import_tasks | Static task import |
| include_tasks | Dynamic task import |
| changed_when | Override changed status |
| failed_when | Override failure status |
| package | Cross-platform package management |
| import_role | Static role import |
| include_role | Dynamic role import |
| setup | Gather Ansible facts |
| Check Mode | Dry run |
| Diff Mode | Show configuration changes |
| async | Run task in background |
| poll | Check async task status |

---

# Best Practices

- Use **serial** for production deployments.
- Prefer **package** over OS-specific package modules when possible.
- Use **changed_when** for read-only tasks.
- Use **failed_when** with registered outputs for custom validations.
- Use **include_tasks** for dynamic task inclusion.
- Use **import_tasks** for static task inclusion.
- Use **Check Mode** before production deployments.
- Use **Diff Mode** to verify configuration changes.
- Use **Async & Poll** for long-running operations.

---

# Summary

- **serial** enables rolling deployments.
- **include_tasks** and **include_role** provide dynamic inclusion.
- **import_tasks** and **import_role** provide static inclusion.
- **changed_when** customizes changed status.
- **failed_when** customizes failure conditions.
- **package** is a generic package management module.
- **setup** gathers system facts.
- **Check Mode** performs dry-run execution.
- **Diff Mode** previews configuration changes.
- **Async & Poll** execute long-running tasks asynchronously.