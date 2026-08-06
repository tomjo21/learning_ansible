# Ansible Tags

## What are Tags?

Ansible **Tags** are labels assigned to tasks, plays, blocks, or roles that allow selective execution of parts of a playbook.

Instead of executing an entire playbook, tags let you execute only the required tasks.

---

# Why Do We Need Tags?

Consider a playbook with 150 tasks.

```
Install Docker
↓
Install Nginx
↓
Install Java
↓
Copy Website
↓
Restart Nginx
↓
Configure Firewall
↓
Create Users
```

Suppose only `index.html` changes.

Running the complete playbook again would unnecessarily execute tasks such as:

- Install Docker
- Install Java
- Configure Firewall
- Create Users

Using **Tags**, you can execute only the deployment-related tasks.

---

# Benefits of Tags

- Execute only required tasks.
- Reduce deployment time.
- Simplify debugging.
- Improve playbook maintainability.
- Useful for CI/CD pipelines.
- Avoid unnecessary task execution.

---

# Basic Syntax

Assign a tag to a task.

```yaml
- name: Install Apache
  ansible.builtin.apt:
    name: apache2
    state: present
  tags:
    - install
```

---

# Multiple Tags

A task can have multiple tags.

```yaml
- name: Restart Apache
  ansible.builtin.service:
    name: apache2
    state: restarted
  tags:
    - deploy
    - restart
    - apache
```

A task executes if **any one** of its tags matches the requested tag.

---

# Running Tagged Tasks

Run only tasks with the `install` tag.

```bash
ansible-playbook playbook.yml --tags install
```

Run multiple tags.

```bash
ansible-playbook playbook.yml --tags "install,deploy"
```

> Multiple tags are treated as **OR**, not **AND**.

---

# Skipping Tagged Tasks

Skip all tasks with the `deploy` tag.

```bash
ansible-playbook playbook.yml --skip-tags deploy
```

All other tasks will execute.

---

# How Ansible Processes Tags

Ansible executes tasks sequentially.

For each task, it checks whether the task matches the requested tag(s).

```
Task
↓

Has requested tag?

YES → Execute

NO → Skip
```

Tags act as **filters**, not schedulers.

---

# Execution Without Tags

Running a playbook normally:

```bash
ansible-playbook playbook.yml
```

All tasks execute regardless of whether they have tags.

---

# Execution with `--tags`

Only tasks containing the specified tag execute.

Example:

```
Install Docker      [install]
Copy Website        [deploy]
Restart Apache      [restart]
```

Command:

```bash
ansible-playbook playbook.yml --tags deploy
```

Result:

```
Install Docker      ❌
Copy Website        ✅
Restart Apache      ❌
```

---

# Execution with `--skip-tags`

Example:

```
Install Docker      [install]
Copy Website        [deploy]
Restart Apache      [restart]
```

Command:

```bash
ansible-playbook playbook.yml --skip-tags deploy
```

Result:

```
Install Docker      ✅
Copy Website        ❌
Restart Apache      ✅
```

---

# Untagged Tasks

Suppose a task has no tags.

```yaml
- name: Install Docker
  ansible.builtin.apt:
    name: docker.io
    state: present
```

Running

```bash
ansible-playbook playbook.yml --tags deploy
```

will skip this task because it does not match the requested tag.

---

# Special Tags

## always

Tasks tagged with `always` execute even when tag filtering is used, unless explicitly skipped.

```yaml
- name: Gather Facts
  ansible.builtin.setup:
  tags:
    - always
```

Skip it explicitly:

```bash
ansible-playbook playbook.yml --skip-tags always
```

---

## never

Tasks tagged with `never` do not execute during a normal playbook run.

```yaml
- name: Delete Logs
  ansible.builtin.file:
    path: /var/log/app.log
    state: absent
  tags:
    - never
```

Execute explicitly:

```bash
ansible-playbook playbook.yml --tags never
```

A task with multiple tags executes if **any requested tag matches**.

Example:

```yaml
tags:
  - never
  - cleanup
```

Running

```bash
ansible-playbook playbook.yml --tags cleanup
```

executes the task.

---

# Play-Level Tags

Tags can be assigned to an entire play.

```yaml
---
- hosts: webservers
  tags:
    - install

  tasks:
    - name: Install Apache
      ansible.builtin.apt:
        name: apache2
        state: present
```

All tasks inside the play inherit the `install` tag.

---

# Role-Level Tags

Tags can also be assigned to roles.

```yaml
---
- hosts: all

  roles:
    - role: apache
      tags:
        - webserver
```

Every task inside the `apache` role inherits the `webserver` tag.

---

# Tag Inheritance

Tags assigned at the play or role level are inherited by tasks.

Example:

Play tag:

```text
install
```

Task tag:

```text
docker
```

Effective task tags:

```text
install
docker
```

Inherited tags are **added**, not replaced.

---

# List Available Tags

Display all tags used in a playbook.

```bash
ansible-playbook playbook.yml --list-tags
```

No tasks are executed.

---

# List Tasks

Display the tasks that would execute.

```bash
ansible-playbook playbook.yml --list-tasks
```

Combine with tags.

```bash
ansible-playbook playbook.yml --tags deploy --list-tasks
```

This displays only the tasks matching the `deploy` tag.

---

# Best Practices

- Use meaningful tag names.
- Group related tasks under the same tag.
- Use play-level or role-level tags to reduce repetition.
- Avoid assigning unique tags to every task.
- Reserve `always` for essential tasks.
- Reserve `never` for dangerous or rarely used operations.

---

# Common Mistakes

❌ Assuming tags change execution order.

✔ Tags only filter tasks; execution remains sequential.

---

❌ Assuming multiple tags are treated as AND.

✔ Multiple tags use OR logic.

---

❌ Assuming `never` means "cannot execute."

✔ `never` tasks execute when explicitly requested.

---

❌ Forgetting that play-level and role-level tags are inherited by tasks.

---

# Interview Questions

### What are Ansible Tags?

Labels used to selectively execute or skip tasks, plays, blocks, or roles.

---

### Are Tags schedulers?

No.

They are filters.

---

### Can a task have multiple tags?

Yes.

---

### Are multiple tags treated as AND or OR?

OR.

---

### What happens to untagged tasks when `--tags` is used?

They are skipped.

---

### What is the difference between `--tags` and `--skip-tags`?

- `--tags` executes only matching tasks.
- `--skip-tags` executes all tasks except the matching ones.

---

### What is the purpose of the `always` tag?

It ensures a task executes even when tag filtering is used, unless explicitly skipped.

---

### What is the purpose of the `never` tag?

It prevents a task from running during normal execution. The task runs only when one of its tags is explicitly requested.

---

### Do play-level and role-level tags affect tasks?

Yes.

Tasks inherit tags from their play and role.

---

# Summary

- Tags are labels used for selective task execution.
- Tags filter tasks without changing execution order.
- Tasks execute if **any** requested tag matches.
- `--tags` acts as a whitelist.
- `--skip-tags` acts as a blacklist.
- Tasks inherit tags from plays and roles.
- Use `--list-tags` to view available tags.
- Use `--list-tasks` to preview task execution.