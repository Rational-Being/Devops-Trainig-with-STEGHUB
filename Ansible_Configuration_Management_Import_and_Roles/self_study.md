# Learnings from Ansible Playbooks Reuse

The reuse of Ansible artifacts greatly simplifies the management of complex automation tasks, allowing for modular, maintainable, and scalable playbooks. This documentation highlights core concepts and best practices for organizing reusable components in Ansible.

## Types of Reusable Artifacts in Ansible

Ansible allows us to reuse several artifacts, each serving a unique purpose. These artifacts provide modularity, making our playbooks cleaner and easier to manage.

- **Variable Files**: Contain only variables. Typically organized by environment or function (e.g., `prod.yml`, `db_variables.yml`).
  
- **Task Files**: Contain only tasks, which can be referenced and included in other playbooks for reuse (e.g., `tasks/install.yml`).
  
- **Playbooks**: At least one play that can include both variables and tasks. Designed to be modular and can reference other artifacts.
  
- **Roles**: A collection of related tasks, variables, defaults, and handlers organized in a standardized structure (tasks, handlers, defaults, files, etc.) under a defined directory. This is the most comprehensive way to group reusable components.

## Re-using Playbooks

Playbooks can be reused by importing them into other playbooks, making it easy to structure complex workflows.

### Importing Playbooks

The `import_playbook` directive allows you to combine multiple playbooks into a main playbook. This method is static and processed before runtime.

**Example:**
```yaml
- import_playbook: webservers.yml
- import_playbook: databases.yml
```

### Dynamic Playbook Importing

Dynamic importing allows playbook paths to be passed as variables, enhancing flexibility in which playbooks to include based on variable values.

**Example:**
```yaml
- import_playbook: "/path/to/{{ import_from_extra_var }}"
```

### Best Practice Tips
- Use `import_playbook` for static reuse when playbook structure is predictable.
- Use variables for dynamic playbook selection when needed based on environment or conditions.

## When to Use Roles

Roles allow the division of complex configurations into smaller, well-organized units. They are especially beneficial for managing larger projects as they separate tasks, variables, and handlers into different folders.

### Advantages of Using Roles
- **Modularity**: Simplifies tasks by breaking them down into role-based files and directories.
- **Ease of Maintenance**: Roles are easier to update or replace without impacting the entire playbook.
- **Shareability**: Roles can be shared across projects, making them ideal for reusable and community-driven configurations.

## Re-using Files and Roles in Playbooks

Ansible provides both **static** and **dynamic** methods for including files, which help control task execution and resource management.

### Dynamic Reuse with `include_*`
Dynamic reuse allows tasks or files to be included at runtime and is more adaptable to varying conditions, such as looped iterations.

**Example of Dynamic Task Inclusion:**
```yaml
- include_tasks: tasks.yml
```

### Static Reuse with `import_*`
Static reuse includes tasks or files before runtime, making it faster and more resource-efficient. It is ideal when the included file does not require changes based on runtime conditions.

**Example of Static Task Inclusion:**
```yaml
- import_tasks: tasks.yml
```

### Best Practice Tips
- Use `include_*` for tasks that need runtime flexibility or need to be looped.
- Use `import_*` for static tasks or files that remain consistent and do not require condition-based execution.

## Comparing Dynamic and Static Reuse

Both reuse types have distinct advantages. Understanding these differences can help optimize playbook execution.

- **Dynamic (Include)**:
  - Processed at runtime.
  - Can respond to the results of previous tasks.
  - Suitable for tasks requiring looping or variable conditions.

- **Static (Import)**:
  - Pre-processed before execution.
  - Not impacted by preceding tasks, making it faster.
  - Ideal for predictable, consistent task structures without loops.

## Re-using Tasks as Handlers

Handlers respond to changes in state, typically to restart or reload services. Ansible supports both dynamic and static approaches for reusing handlers, allowing flexibility depending on requirements.

### Dynamic Handler with `include_tasks`
Use `include_tasks` in handlers when you want runtime flexibility.

**Example:**
```yaml
- name: Trigger an included handler
  handlers:
    - name: Restart services
      include_tasks: restarts.yml
  tasks:
    - command: "true"
      notify: Restart services
```

### Static Handler with `import_tasks`
Use `import_tasks` in handlers for faster execution without runtime adaptability.

**Example:**
```yaml
- name: Trigger an imported handler
  handlers:
    - name: Restart services
      import_tasks: restarts.yml
  tasks:
    - command: "true"
      notify: Restart apache
    - command: "true"
      notify: Restart mysql
```
