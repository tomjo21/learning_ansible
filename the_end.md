# Enterprise & Advanced Ansible

This document covers enterprise-level Ansible concepts, performance optimization, testing, plugin architecture, and specialized automation topics.

---

# 1. AWX / Ansible Automation Platform (AAP)

## What is AWX?

AWX is the **open-source web-based interface and REST API** for Ansible.

It is the upstream project for **Red Hat Ansible Automation Platform (AAP)** (formerly Ansible Tower).

```
AWX (Open Source)
        │
        ▼
Ansible Automation Platform (Enterprise)
```

---

## Features

- Web UI
- REST API
- Organizations
- Users & Teams
- RBAC (Role-Based Access Control)
- Credentials Management
- Inventory Management
- Projects (Git Integration)
- Job Templates
- Workflows
- Scheduling
- Notifications
- Job History & Logging
- Execution Environments

---

## Architecture

```
GitHub
   │
   ▼
AWX
   │
   ├── Inventory
   ├── Credentials
   ├── Projects
   ├── Job Templates
   └── Execution Environment
            │
            ▼
Managed Hosts
```

---

# 2. Execution Environments (EE)

## Definition

Execution Environments are **containerized Ansible runtimes** that package:

- Python
- Ansible
- Collections
- Required SDKs
- Dependencies

This ensures consistent execution across environments.

---

## Tools

- ansible-builder
- ansible-navigator

---

## Benefits

- Dependency isolation
- Reproducible automation
- Consistent runtime
- Container-based execution

---

# 3. Ansible Navigator

Modern CLI for interacting with Execution Environments.

Example:

```bash
ansible-navigator run playbook.yml
```

Supports:

- Interactive mode
- Stdout mode
- Execution Environments
- Collections

---

# 4. Strategy Plugins

Strategy plugins determine how Ansible executes tasks across hosts.

---

## linear (Default)

All hosts execute Task 1 before moving to Task 2.

```
Task1
↓

All Hosts

↓

Task2
```

---

## free

Hosts execute independently.

```
Host1 → Task1 → Task2 → Task3

Host2 → Task1
```

---

## debug

Useful for debugging playbooks.

---

# 5. forks

Controls parallel execution.

Default:

```ini
forks = 5
```

CLI

```bash
ansible-playbook play.yml --forks 20
```

---

## serial vs forks

| serial | forks |
|---------|--------|
| Batch Size | Parallel Workers |
| Deployment Strategy | Execution Performance |

---

# 6. ansible.cfg

Main configuration file.

---

## Configuration Precedence

```
ANSIBLE_CONFIG

↓

Current Directory

↓

Home Directory (~/.ansible.cfg)

↓

/etc/ansible/ansible.cfg
```

---

## Common Settings

```ini
[defaults]
inventory=inventory
forks=20
host_key_checking=False
timeout=30
remote_user=ubuntu
roles_path=./roles
```

---

# 7. Meta Module

Special module for controlling play execution.

---

## Common Actions

```yaml
- meta: flush_handlers
```

Execute handlers immediately.

```yaml
- meta: end_play
```

Stop current play.

```yaml
- meta: clear_facts
```

Remove gathered facts.

```yaml
- meta: refresh_inventory
```

Reload inventory.

---

# 8. Error Handling Keywords

## ignore_errors

```yaml
ignore_errors: yes
```

Continue execution even if the task fails.

---

## ignore_unreachable

Continue even if a host is unreachable.

---

## force_handlers

Run handlers even when failures occur.

---

## any_errors_fatal

Stop execution on every host if one host fails.

---

## max_fail_percentage

```yaml
max_fail_percentage: 30
```

Stop play if failures exceed the specified percentage.

---

# 9. Retries

Retry failed tasks.

```yaml
- command: curl localhost:8080
  register: result

  until: result.rc == 0
  retries: 5
  delay: 10
```

---

## Keywords

- until
- retries
- delay

---

## Use Cases

- Waiting for APIs
- Database startup
- Kubernetes Pods
- Docker Containers

---

# 10. ansible-lint

Static analysis tool for Ansible playbooks.

---

## Install

```bash
pip install ansible-lint
```

---

## Run

```bash
ansible-lint playbook.yml
```

---

## Detects

- YAML issues
- Deprecated modules
- Best practice violations
- Naming issues
- Unsafe tasks

---

# 11. Molecule

Testing framework for Ansible Roles.

---

## Workflow

```
Create

↓

Converge

↓

Verify

↓

Destroy
```

---

## Commands

```bash
molecule create
molecule converge
molecule verify
molecule destroy
molecule test
```

---

# 12. Connection Plugins

Determine how Ansible connects to managed hosts.

---

## Common Plugins

- ssh (Default)
- local
- docker
- podman
- winrm

---

# 13. Become Plugins

Privilege escalation.

```yaml
become: true
```

---

## become_user

```yaml
become_user: root
```

---

## become_method

Examples:

- sudo
- su
- pbrun

---

# 14. Inventory Plugins

Modern dynamic inventory.

Examples:

- AWS
- Azure
- GCP
- VMware
- Kubernetes

Example:

```yaml
plugin: amazon.aws.aws_ec2
```

---

# 15. delegate_facts

Stores facts on the delegated host.

```yaml
- setup:
  delegate_to: db01
  delegate_facts: true
```

---

# 16. local_action

Legacy equivalent of

```yaml
delegate_to: localhost
```

Example

```yaml
- local_action:
    module: shell
    cmd: echo "Hello"
```

Modern recommendation:

```yaml
delegate_to: localhost
```

---

# 17. Writing Custom Modules

Develop custom Ansible modules using Python.

Typical workflow:

```
Receive Arguments

↓

Execute Logic

↓

Return JSON
```

---

# 18. Writing Plugins

Common plugin types:

- Filter
- Lookup
- Callback
- Action
- Cache
- Inventory
- Connection

---

# 19. Collections Development

Collection Structure

```
collection/
├── plugins/
├── roles/
├── docs/
├── tests/
└── galaxy.yml
```

Commands

```bash
ansible-galaxy collection init
ansible-galaxy collection build
ansible-galaxy collection publish
```

---

# 20. Collections Ecosystem

### Galaxy

Public community collections.

### Automation Hub

Enterprise-certified collections.

### Private Collections

Internal company collections.

---

# 21. Environment Variables

Pass environment variables to tasks.

```yaml
environment:
  JAVA_HOME: /usr/lib/jvm/java-17
  MAVEN_HOME: /opt/maven
```

---

# 22. Fact Caching

Avoid gathering facts every run.

Supported Backends

- Memory
- JSON
- Redis
- Memcached

Benefits

- Faster execution
- Reduced SSH traffic
- Better scalability

---

# 23. Performance Optimization

Common techniques:

- Increase forks
- Enable pipelining
- SSH Multiplexing
- Fact Caching
- Disable gather_facts when unnecessary

Example

```yaml
gather_facts: false
```

---

# 24. Ansible Pull

Default Model

```
Control Node

↓

Managed Hosts
```

Pull Model

```
Git Repository

↓

Managed Host

↓

ansible-pull
```

Useful for:

- IoT
- Edge Devices
- Firewalled Systems

---

# 25. Network Automation

Supported Vendors

- Cisco IOS
- Cisco NX-OS
- Juniper
- Arista
- Palo Alto
- Fortinet

Protocols

- SSH
- NETCONF
- HTTPAPI

Example Modules

```yaml
cisco.ios.ios_config

junipernetworks.junos.junos_config
```

Use Cases

- VLANs
- Routing
- ACLs
- Interface Configuration
- Firewall Policies

---

# 26. Windows Automation

Connection Protocol

```
WinRM
```

Common Modules

- win_copy
- win_shell
- win_command
- win_service
- win_package
- win_file
- win_user

Example

```yaml
- name: Install Chrome
  win_package:
    path: chrome.msi
```

---

# Interview Cheat Sheet

| Topic | Key Point |
|--------|-----------|
| AWX | Web UI + REST API for Ansible |
| AAP | Enterprise version based on AWX |
| EE | Containerized runtime |
| Navigator | Modern CLI |
| Strategy | linear, free, debug |
| forks | Parallel execution |
| ansible.cfg | Main configuration file |
| Meta | Control play execution |
| Retry | until + retries + delay |
| ansible-lint | Static analysis |
| Molecule | Role testing |
| Connection Plugins | How Ansible connects |
| Become Plugins | Privilege escalation |
| Inventory Plugins | Dynamic inventory |
| delegate_facts | Store facts on delegated host |
| local_action | Legacy delegate_to localhost |
| Custom Modules | Extend Ansible using Python |
| Plugins | Extend Ansible functionality |
| Collections | Package roles, modules, plugins |
| Environment | Task-specific environment variables |
| Fact Caching | Improve performance |
| Performance | forks, pipelining, caching |
| Ansible Pull | Pull-based automation |
| Network Automation | Automate network devices |
| Windows Automation | Manage Windows via WinRM |

---

# Summary

- AWX provides enterprise management for Ansible.
- Execution Environments package dependencies into containers.
- Navigator is the modern CLI for Ansible.
- Strategy plugins and forks control execution behavior.
- ansible.cfg centralizes configuration.
- Meta and error handling keywords improve execution control.
- Molecule and ansible-lint improve quality.
- Connection, Become, and Inventory Plugins extend automation.
- Custom Modules and Plugins enable extensibility.
- Collections package reusable Ansible content.
- Fact Caching and Performance Optimization improve scalability.
- Ansible Pull supports pull-based automation.
- Ansible supports Linux, Windows, and Network automation through dedicated modules and plugins.