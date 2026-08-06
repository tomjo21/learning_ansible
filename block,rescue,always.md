# Ansible Blocks, Rescue & Always

## What are Blocks, Rescue & Always?

`block`, `rescue`, and `always` are Ansible constructs used for **grouping tasks and handling errors**.

They are similar to exception handling in programming languages.

| Programming | Ansible |
|-------------|----------|
| `try` | `block` |
| `catch` | `rescue` |
| `finally` | `always` |

---

# Why Use Them?

Suppose you have the following tasks:

```
Install Docker
↓

Copy Configuration
↓

Restart Docker
↓

Deploy Application
```

If **Copy Configuration** fails, the playbook stops immediately.

Using `block`, `rescue`, and `always`, you can:

- Handle failures gracefully
- Perform rollback operations
- Display custom error messages
- Execute cleanup tasks regardless of success or failure

---

# Syntax

```yaml
tasks:

  - block:

      - name: Task 1
        ...

      - name: Task 2
        ...

    rescue:

      - name: Execute if block fails
        ...

    always:

      - name: Execute every time
        ...
```

---

# Simple Example

```yaml
---
- hosts: all

  tasks:

    - block:

        - name: List Home Directory
          command: ls /home

      rescue:

        - name: Display Failure Message
          debug:
            msg: "Command failed."

      always:

        - name: Display Completion Message
          debug:
            msg: "Execution completed."
```

---

## Successful Execution

```
List Home Directory     ✅

Failure Message         ❌

Completion Message      ✅
```

---

## Failed Execution

If the command is changed to:

```yaml
command: ls /invalid_directory
```

Execution:

```
List Home Directory     ❌

Failure Message         ✅

Completion Message      ✅
```

---

# Real-World Example

```yaml
- block:

    - name: Copy Nginx Configuration
      copy:
        src: nginx.conf
        dest: /etc/nginx/nginx.conf

    - name: Restart Nginx
      service:
        name: nginx
        state: restarted

  rescue:

    - name: Restore Backup Configuration
      copy:
        src: nginx.conf.backup
        dest: /etc/nginx/nginx.conf

    - name: Restart Nginx Again
      service:
        name: nginx
        state: restarted

  always:

    - name: Notify Deployment Status
      debug:
        msg: "Deployment process completed."
```

---

# Execution Flow

```
Block
│
├── Success
│      │
│      ▼
│    Always
│
└── Failure
       │
       ▼
    Rescue
       │
       ▼
     Always
```

---

# Important Points

- A block can contain multiple tasks.
- If any task inside the block fails, the remaining tasks in the block are skipped.
- `rescue` executes only when the block fails.
- `always` executes regardless of success or failure.
- If `rescue` also fails, `always` still executes.

---

# Best Practices

- Group related tasks inside a block.
- Use `rescue` for rollback and error handling.
- Use `always` for cleanup tasks such as:
  - Removing temporary files
  - Closing connections
  - Sending notifications
  - Releasing locks
- Avoid placing unrelated tasks in the same block.

---

# Common Mistakes

❌ Assuming `rescue` runs after every block.

✔ It runs only if the block fails.

---

❌ Assuming `always` runs only after success.

✔ It runs after both success and failure.

---

❌ Assuming tasks continue after a failure inside a block.

✔ Remaining tasks in the block are skipped once a task fails.

---

# Interview Questions

### What is the purpose of `block`?

It groups multiple tasks together for common error handling.

---

### When does `rescue` execute?

Only when any task inside the block fails.

---

### When does `always` execute?

After every block execution, regardless of success or failure.

---

### What happens if a task inside the block fails?

- Remaining tasks in the block are skipped.
- `rescue` executes.
- `always` executes.

---

### Can a block contain multiple tasks?

Yes.

---

### Does `always` execute if `rescue` also fails?

Yes.

---

# Summary

- `block` groups related tasks.
- `rescue` handles failures.
- `always` performs cleanup regardless of the result.
- Similar to `try-catch-finally` in programming.
- Useful for rollback, cleanup, notifications, and production deployments.