# Ansible Roles

An Ansible role is a reusable, self-contained unit of automation used to organize and manage tasks, variables, files, templates, and handlers in a structured way.

Roles help encapsulate and modularize the logic and configuration needed to manage a particular system or application component.

This modular approach promotes reusability, maintainability, and consistency across different playbooks and environments.

## Key Components of an Ansible Role

### Tasks

The main list of actions that the role performs.

### Handlers

Tasks that are triggered by changes in other tasks, typically used for actions such as restarting services.

### Files

Static files that need to be transferred to managed hosts.

### Templates

Jinja2 templates that are rendered and transferred to managed hosts.

### Vars

Variables used within the role.

### Defaults

Default variables for the role that can be overridden by higher-precedence variables.

### Meta

Metadata about the role, including dependencies on other roles.

### Library

Custom modules or plugins used within the role.

### Module Defaults

Default parameters for Ansible modules used by the role.

### Lookup Plugins

Custom lookup plugins used within the role.

## Directory Structure of an Ansible Role

An Ansible role follows a standardized directory structure:

```text
<role_name>/
├── defaults/
│   └── main.yml
├── files/
├── handlers/
│   └── main.yml
├── meta/
│   └── main.yml
├── tasks/
│   └── main.yml
├── templates/
└── vars/
    └── main.yml
```

## Why Use Ansible Roles?

### Modularity

Roles allow you to break down complex playbooks into smaller, reusable components. Each role is responsible for a specific part of the configuration or deployment.

### Reusability

Once created, roles can be reused across multiple playbooks and projects, reducing duplicate code and saving development time.

### Maintainability

Organizing related tasks into roles makes infrastructure code easier to maintain. Changes can be made in one place and automatically applied wherever the role is used.

### Readability

Roles keep playbooks clean and concise by moving implementation details into well-structured directories.

### Collaboration

Roles enable team members to work independently on different infrastructure components without interfering with each other's work.

### Consistency

Using roles ensures that the same configuration and deployment procedures are applied consistently across multiple environments, reducing configuration drift.