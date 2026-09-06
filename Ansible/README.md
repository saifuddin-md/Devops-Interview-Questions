# Ansible Interview Questions and Answers

## 1. What did you actually do with Ansible?

**A/1:**  
I used Ansible to manage the configuration of multiple Linux servers. We maintained server groups in the inventory and used playbooks to perform repeatable administration tasks.

Examples:
- Package installation/removal
- Service management
- User/group management
- File and directory management
- Configuration changes
- Permissions management
- OS information gathering
- Patching

**A/2:**  
I used Ansible to automate provisioning of an Ubuntu LEMP server, including Nginx, MySQL, PHP, and phpMyAdmin installation, Nginx configuration, PHP application deployment, database and user creation, service management, and final application configuration.

---

## 2. Can you talk about an Ansible playbook that you wrote and how it helped your company?

**Answer:**  
We had multiple Linux servers running in our on-premises environment. We needed to install and configure the Wazuh agent on those servers for security monitoring.

Instead of manually logging into every server, I created an Ansible playbook that copied the installation script, which was provided by the security team, to the remote servers, executed it, and then cleaned up the temporary script afterward.

This helped reduce manual effort, made the installation consistent across servers, and allowed us to deploy the Wazuh agent to multiple servers quickly.

---

## 3. What is `register`?

**Answer:**  
`register` captures the result of an Ansible task and stores it in a variable so that I can use the result in later tasks.

For example, I can register the result of a command and access its `stdout`, return code, or failure status.

---

## 4. What is `when`?

**Answer:**  
`when` is used for conditional task execution. Ansible runs the task only when the specified condition evaluates to true.

For example, after checking whether `/var/run/reboot-required` exists, I can use `when: reboot_required.stat.exists` to reboot the server only when necessary.

---

## 5. How do you patch 100 production servers safely?

**Answer:**  
I would not patch all 100 production servers simultaneously. I would first group the servers by application role and dependency, perform pre-checks, and start with a small canary group.

I would use Ansible `serial` to control the rollout, for example, patching 1–5 servers initially.

After patching, I would verify:
- Connectivity
- Reboot status
- Kernel version
- Critical services
- Application health

If the canary servers are healthy, I would gradually increase the batch size.

If a server fails, I would stop or pause the rollout, investigate the issue, and only continue after validation. I would also use `--limit` when necessary and maintain logs and change records for the patching activity.

---

## 6. How do you handle a server that fails during patching?

**Answer:**  
If a server fails during patching, I first check the Ansible task output and logs to identify where the failure occurred.

I verify whether the server is still reachable and check:
- Package-manager errors
- Disk space
- Repository connectivity
- Service status

I don't immediately rerun the entire playbook blindly. If the issue is recoverable, I fix the root cause and rerun the failed task.

For production, I use `serial` so that servers are patched in controlled batches. This prevents a failure on one server from affecting the entire environment.

After successful patching, I perform post-patch validation before continuing with the remaining servers.

---

## 7. What is the difference between `copy` and `template`?

**Answer:**  
The `copy` module is used when I want to transfer a static file from the Ansible controller to the managed server without modifying its contents.

The `template` module is used when the file contains dynamic values or Jinja2 expressions.

For example, I would use `template` for an Nginx or application configuration where different servers need different IP addresses, hostnames, ports, or environment-specific values.

---

## 8. What is the difference between `command` and `shell`?

**Answer:**  
The main difference is that `command` executes a command directly, while `shell` executes the command through the server's shell, usually `/bin/sh`.

I prefer `command` when shell features are not required. I use `shell` when I specifically need shell functionality such as pipes, redirection, or shell operators.

---

## 9. What are roles?

**Answer:**  
Roles are a way to organize playbooks and tasks into reusable units.

They help keep Ansible code structured and make automation easier to reuse and maintain across different environments.

---

## 10. What is Ansible Galaxy?

**Answer:**  
Ansible Galaxy is a community hub for Ansible roles and collections. It is a central repository where we can find, share, and reuse pre-written roles and collections created by the Ansible community.

---

## 11. What is Ansible Tower?

**Answer:**  
Ansible Tower is a web-based interface that provides a graphical dashboard for managing Ansible automation.

It provides features such as centralized job execution, scheduling, access control, credentials management, and monitoring.

Today, Ansible Tower is part of **Red Hat Ansible Automation Platform**.

---

## 12. What is Ansible Vault?

**Answer:**  
Ansible Vault is a feature of Ansible that allows us to keep sensitive data, such as passwords, API keys, and other secrets, in encrypted form.

Common commands are:

```bash
ansible-vault create pass.yaml
```

Creates a new encrypted file.

```bash
ansible-vault edit pass.yaml
```

Edits an encrypted file.

```bash
ansible-vault rekey pass.yaml
```

Changes the Vault password.

```bash
ansible-vault encrypt pass.yaml
```

Encrypts an existing file.

```bash
ansible-vault decrypt pass.yaml
```

Decrypts an encrypted file.

---

## 13. What is Ansible inventory and what are its types?

**Answer:**  
An Ansible inventory is a list of managed nodes or servers organized into groups.

There are two common types:

### Static Inventory
A manually created inventory file where host and group information is written manually. It is commonly stored in INI or YAML format.

### Dynamic Inventory
Ansible automatically gets the list of servers from cloud platforms or other infrastructure sources. It can use a script or plugin to automatically retrieve servers from AWS, Azure, VMware, and other platforms.

---

## 14. What is a notifier?

**Answer:**  
`notify` is an Ansible task attribute used to trigger a handler when the task reports a change.

For example:

```yaml
- name: Update nginx configuration
  ansible.builtin.template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Restart nginx
```

Here, `notify` triggers the `Restart nginx` handler only when the template task makes a change.

---

## 15. What is a handler?

**Answer:**  
A handler is a special task that is executed only when it is notified by another task.

For example, when an Nginx configuration file changes, I can notify a handler to restart or reload Nginx.

```yaml
handlers:
  - name: Restart nginx
    ansible.builtin.service:
      name: nginx
      state: restarted
```

---

## 16. What is an ad-hoc command?

**Answer:**  
Ansible ad-hoc commands are used for quick, one-time tasks instead of creating a complete playbook. They are useful for simple administration and troubleshooting.

For example:

```bash
ansible demo -a "ls"
```

Another example:

```bash
ansible all -b -m yum -a "name=httpd state=present"
```

For repeatable configuration management and deployment, I would normally use playbooks rather than ad-hoc commands.

---

## 17. What is a module?

**Answer:**  
An Ansible module is like a command or function that performs one specific automation task, while a playbook combines multiple module calls to automate a complete workflow.

Examples of modules include:
- `copy`
- `yum`
- `package`
- `service`
- `user`
- `file`
- `template`

Example:

```bash
ansible demo -b -m yum -a "name=httpd state=present"
```

```bash
ansible all -b -m copy -a "src=file dest=/tmp"
```

---

## 18. How do you handle errors in Ansible?

**Answer:**  
I handle errors in Ansible based on the type of failure and whether the task is allowed to fail.

For example, `ignore_errors: yes` allows the playbook to continue even if a particular task fails.

```yaml
- name: Run a command
  ansible.builtin.command: /some/command
  ignore_errors: yes
```

However, I don't use `ignore_errors` blindly. For production automation, I prefer to understand the failure and use appropriate error-handling mechanisms such as `failed_when`, `changed_when`, `block`, `rescue`, and `always` when required.

---

## 19. What are Facts in Ansible?

**Answer:**  
Ansible facts are system information that Ansible automatically collects from managed hosts before executing a playbook.

They can include information such as:
- Operating system
- IP addresses
- Hostname
- CPU information
- Memory
- Disk information

For example:

```yaml
- hosts: all
  gather_facts: false

  tasks:
    - name: Display hostname
      ansible.builtin.debug:
        var: ansible_hostname
```

If I don't need system facts, I can set `gather_facts: false` to save execution time.

---

## 20. Write a playbook to create users on multiple servers.

**Answer:**

```yaml
---
- name: Create users on multiple servers
  hosts: all
  become: yes

  tasks:
    - name: Create user john
      ansible.builtin.user:
        name: john
        state: present

    - name: Create user alice
      ansible.builtin.user:
        name: alice
        state: present
```

This playbook connects to all servers in the inventory, uses privilege escalation, and creates the `john` and `alice` users if they do not already exist.