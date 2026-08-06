# Ansible `delegate_to` & `run_once`

## delegate_to

### What is `delegate_to`?

By default, Ansible executes tasks on the target hosts defined in the play.

`delegate_to` allows a specific task to execute on a different host.

Common delegation targets include:

- `localhost`
- Load Balancer
- Bastion Host
- Monitoring Server
- DNS Server

---

## Why Use `delegate_to`?

Some tasks should not execute on the managed hosts.

Examples:

- Remove a server from a Load Balancer
- Add a server back to a Load Balancer
- Update DNS records
- Send notifications
- Create backups on the control node

---

## Syntax

```yaml
- name: Create Backup
  command: tar -czf backup.tar.gz /var/www
  delegate_to: localhost
```

---

## Simple Example

```yaml
---
- hosts: webservers

  tasks:

    - name: Create Backup
      command: tar -czf backup.tar.gz /tmp
      delegate_to: localhost

    - name: Restart Apache
      service:
        name: apache2
        state: restarted
```

Execution:

```
Control Node
↓

Create Backup

↓

Web Server

↓

Restart Apache
```

Only the **Backup** task is delegated.

---

## Real-World Example

```yaml
- hosts: webservers

  tasks:

    - name: Remove Server from Load Balancer
      command: ./remove_server.sh
      delegate_to: localhost

    - name: Deploy Application
      copy:
        src: app.jar
        dest: /opt/app/

    - name: Restart Application
      service:
        name: myapp
        state: restarted

    - name: Add Server Back to Load Balancer
      command: ./add_server.sh
      delegate_to: localhost
```

---

## Important Points

- `delegate_to` affects only the current task.
- The next task executes on the original target host unless `delegate_to` is specified again.
- It changes **where** a task executes, not how many times.

---

## Common Mistakes

❌ Assuming the entire play executes on `localhost`.

✔ Only the delegated task executes on `localhost`.

---

❌ Forgetting that subsequent tasks return to the original target host.

---

## Interview Questions

### Does `delegate_to` change the play's target hosts?

No.

It only changes the execution host for the current task.

---

### Can `delegate_to` point to another remote server?

Yes.

Example:

```yaml
delegate_to: lb01
```

---

# run_once

## What is `run_once`?

By default, Ansible executes every task once for every host in the play.

`run_once` tells Ansible to execute the task only once, regardless of the number of hosts.

---

## Why Use `run_once`?

Some operations should occur only once.

Examples:

- Download artifacts
- Generate reports
- Create backups
- Generate SSL certificates
- Send notifications

---

## Syntax

```yaml
- name: Download Artifact
  get_url:
    url: https://example.com/app.jar
    dest: /tmp/app.jar
  run_once: true
```

---

## Simple Example

Inventory:

```
web1
web2
web3
```

Playbook:

```yaml
- hosts: webservers

  tasks:

    - name: Print Message
      debug:
        msg: "Deployment Started"
      run_once: true
```

Output:

```
Deployment Started
```

The task executes only once.

---

## Real-World Example

```yaml
- hosts: webservers

  tasks:

    - name: Download Artifact
      get_url:
        url: https://company.com/app.jar
        dest: /tmp/app.jar
      run_once: true

    - name: Copy Artifact
      copy:
        src: /tmp/app.jar
        dest: /opt/app/

    - name: Restart Application
      service:
        name: myapp
        state: restarted
```

Artifact is downloaded once and then copied to every server.

---

# delegate_to + run_once

These two keywords are commonly used together.

```yaml
- name: Backup Database
  command: tar -czf backup.tar.gz /var/lib/mysql
  delegate_to: localhost
  run_once: true
```

Suppose the inventory contains:

```
web1
web2
web3
```

Execution:

```
localhost

↓

Backup Database (Only Once)
```

Without `run_once`, the backup would execute three times on localhost.

---

## Key Difference

| Feature | Purpose |
|----------|---------|
| `delegate_to` | Changes where the task executes |
| `run_once` | Changes how many times the task executes |

---

## Best Practices

- Use `delegate_to` for operations that should execute on another host.
- Combine `delegate_to` with `run_once` for one-time operations on the control node.
- Keep delegated tasks independent from target-host tasks.

---

## Common Mistakes

❌ Confusing `delegate_to` with `run_once`.

✔ `delegate_to` changes the execution host.

✔ `run_once` changes the execution count.

---

❌ Assuming `run_once` affects subsequent tasks.

✔ It affects only the task where it is defined.

---

# Interview Questions

### What is `delegate_to`?

It executes a specific task on another host instead of the target host.

---

### What is `run_once`?

It executes a task only once, regardless of the number of target hosts.

---

### Can `run_once` be used without `delegate_to`?

Yes.

---

### Can `delegate_to` be used without `run_once`?

Yes.

---

### Why are `delegate_to` and `run_once` commonly used together?

To execute a one-time task on another host (typically the control node), such as creating backups, downloading artifacts, or sending notifications.

---

# Summary

- `delegate_to` changes the execution host for a task.
- `run_once` changes the number of task executions.
- Both are task-specific.
- They are frequently combined in production deployments for one-time operations on the control node.