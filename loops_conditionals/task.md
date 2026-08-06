# Ansible Assignment

## Task 1

Create **three (3)** EC2 instances on AWS using **Ansible loops**.

- 2 Instances with **Ubuntu** distribution
- 1 Instance with **CentOS** distribution

> **Hint:** Use `connection: local` on the Ansible control node.

---

## Task 2

Set up **passwordless authentication** between the Ansible control node and the newly created EC2 instances.

---

## Task 3

Automate the **shutdown of Ubuntu instances only** using **Ansible conditionals**.

> **Hint:** Use the `when` condition based on Ansible gathered facts (`ansible_distribution`).