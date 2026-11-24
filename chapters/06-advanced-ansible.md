# Unit VI — Advanced Ansible

> **Syllabus unit:** Advanced Ansible<br>
> **Course:** INT333 — DevOps Advance Configuration Management (Session 2026–27)

## 1. Learning objectives

This unit covers Ansible roles and command-line usage; playbook structure and execution; real-world playbook examples; handlers; environment variables; variables; facts; prompts; tags; blocks; and Ansible-based AWS application deployment.

## 2. Ansible roles

A **role** is a standard structure for packaging reusable Ansible automation. Roles make large projects easier to organize and share.

Typical role layout:

```text
roles/
└── webserver/
    ├── defaults/main.yml
    ├── vars/main.yml
    ├── tasks/main.yml
    ├── handlers/main.yml
    ├── templates/
    ├── files/
    ├── meta/main.yml
    └── README.md
```

Common directories:

- `tasks/`: tasks to execute.
- `handlers/`: actions triggered by notifications.
- `templates/`: Jinja2 templates.
- `files/`: static files.
- `defaults/`: low-precedence default variables.
- `vars/`: role variables with higher precedence than defaults.
- `meta/`: role metadata and dependencies.

A role should have a clear responsibility, documented inputs and outputs, and tests. Avoid creating a role that is simply a renamed collection of unrelated tasks.

## 3. Ansible command-line usage

Common commands:

```bash
ansible --version
ansible-inventory -i inventory.ini --list
ansible all -i inventory.ini -m ping
ansible-playbook -i inventory.ini site.yml --syntax-check
ansible-playbook -i inventory.ini site.yml --check --diff
ansible-playbook -i inventory.ini site.yml
```

Options:

- `-i`: selects an inventory source.
- `-m`: selects a module for an ad-hoc command.
- `-a`: supplies module arguments in an ad-hoc command.
- `--syntax-check`: checks playbook syntax.
- `--check`: requests check mode where supported by tasks/modules.
- `--diff`: displays file changes for modules that support it.
- `--limit`: restricts a run to selected hosts.
- `--tags` / `--skip-tags`: selects or skips tagged tasks.

Check mode is not a perfect simulation for every module. Some modules do not support it fully, and custom commands may not predict changes. Review task behavior before trusting a dry run.

## 4. Playbooks: structure

A **playbook** is a YAML file containing one or more plays. A play specifies target hosts and may define variables, privilege escalation and tasks.

Example:

```yaml
---
- name: Verify a local web application
  hosts: local
  connection: local
  gather_facts: false
  tasks:
    - name: Check application health endpoint
      ansible.builtin.uri:
        url: http://127.0.0.1:5000/health
        method: GET
        status_code: 200
```

This example assumes that the application is already running on the control node's localhost port 5000. The address is not the same as a container's internal address unless the port is published to the host.

### Running playbooks

```bash
ansible-playbook -i inventory.ini site.yml --syntax-check
ansible-playbook -i inventory.ini site.yml
```

Always inspect the target inventory and playbook before execution. A playbook can change many hosts at once.

## 5. Real-world playbook examples

Common administration workflows include:

- Install and configure a web server.
- Create users and directories.
- Deploy application files.
- Manage services.
- Verify HTTP endpoints.
- Apply updates in controlled batches.

Illustrative playbook pattern:

```yaml
---
- name: Configure a web server
  hosts: web
  become: true
  tasks:
    - name: Install Nginx
      ansible.builtin.package:
        name: nginx
        state: present

    - name: Ensure Nginx is running and enabled
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true
```

The `package` module abstracts package-manager differences where supported. Service names and package availability still depend on the target OS.

## 6. Handlers

A **handler** is a task that runs when notified by another task. Handlers are commonly used for actions such as reloading a service only when its configuration changes.

```yaml
---
- name: Configure web server
  hosts: web
  become: true
  tasks:
    - name: Install Nginx
      ansible.builtin.package:
        name: nginx
        state: present

    - name: Deploy site configuration
      ansible.builtin.copy:
        src: files/site.conf
        dest: /etc/nginx/conf.d/site.conf
        owner: root
        group: root
        mode: '0644'
      notify: Reload Nginx

  handlers:
    - name: Reload Nginx
      ansible.builtin.service:
        name: nginx
        state: reloaded
```

Handlers normally run at the handler phase after notified tasks. A handler is not executed when no task notifies it. Use `listen` or `meta: flush_handlers` only when their behavior is understood and needed.

## 7. Environment variables

Environment variables can influence commands or applications. Ansible can set environment variables for tasks using the `environment` keyword.

```yaml
- name: Run a diagnostic command with an environment value
  hosts: local
  connection: local
  tasks:
    - name: Print selected environment value
      ansible.builtin.command: /usr/bin/env
      environment:
        APP_ENV: testing
```

Avoid using environment variables as a careless secret store. Secrets should be handled through an approved secret-management method or Ansible Vault, with access controls and careful output handling.

## 8. Variables

Variables make playbooks reusable across environments. Values may be defined in inventories, group/host variables, playbooks, roles, command-line options or external variable files.

Example:

```yaml
app_port: 5000
app_health_url: "http://127.0.0.1:{{ app_port }}/health"
```

Variable precedence can be complex. Keep variable sources simple, documented and predictable. Avoid defining the same variable in many places without a clear reason.

## 9. Facts

**Facts** are information Ansible gathers from managed hosts, such as operating system, hostname, interfaces and memory. Facts can be used in conditions and templates.

Example:

```yaml
- name: Display operating system information
  hosts: all
  tasks:
    - name: Show distribution
      ansible.builtin.debug:
        msg: "Distribution: {{ ansible_facts['distribution'] }}"
```

Fact gathering can be enabled or disabled using `gather_facts`. Facts should be inspected on the target rather than assumed to have identical values across systems.

## 10. Prompts

Ansible supports interactive prompts through `vars_prompt`. Prompts are useful for values an operator should supply at execution time, but interactive prompting is often unsuitable for unattended CI pipelines.

Example:

```yaml
---
- name: Ask for a deployment label
  hosts: local
  connection: local
  vars_prompt:
    - name: deployment_label
      prompt: "Enter a deployment label"
      private: false
  tasks:
    - name: Show selected label
      ansible.builtin.debug:
        var: deployment_label
```

Do not prompt users to enter passwords into logs or reveal secret values. Set `private: true` for sensitive prompts, and consider a managed secret mechanism for automated workflows.

## 11. Tags

Tags allow selected tasks or plays to be run or skipped.

```yaml
- name: Install tools
  ansible.builtin.package:
    name: curl
    state: present
  tags:
    - packages
```

Run selected tags:

```bash
ansible-playbook site.yml --tags packages
ansible-playbook site.yml --skip-tags packages
```

Tags are useful for targeted operations but can cause dependencies to be skipped if a playbook is not designed carefully. Test tagged execution as well as a full run.

## 12. Blocks

Blocks group tasks and support shared directives such as conditions, privilege escalation and error handling. A block may have `rescue` and `always` sections.

```yaml
- name: Demonstrate controlled error handling
  hosts: local
  connection: local
  tasks:
    - name: Run a task with recovery logic
      block:
        - name: Check a known local file
          ansible.builtin.stat:
            path: /tmp/autoops-example.txt
          register: example_file

        - name: Fail when the file is missing
          ansible.builtin.fail:
            msg: "Expected test file is missing"
          when: not example_file.stat.exists
      rescue:
        - name: Record controlled failure
          ansible.builtin.debug:
            msg: "The validation failed and was handled by the rescue section."
      always:
        - name: Record completion of the validation block
          ansible.builtin.debug:
            msg: "Validation block finished."
```

A rescued task failure is handled by the playbook's recovery logic; it should not be used to hide a genuine deployment failure. Capture and report the failure accurately.

## 13. Ansible with AWS for application deployment

The syllabus includes deploying an application to AWS using Ansible. A typical workflow is:

1. Prepare an AWS account and select a region.
2. Create or select an EC2 instance and networking/security configuration.
3. Configure secure access using an SSH key and an approved AWS identity.
4. Add the instance to Ansible inventory.
5. Verify SSH and Python availability.
6. Use playbooks to install application dependencies and deploy the application.
7. Configure and start the service.
8. Verify application health and access controls.

Ansible can configure an existing EC2 instance. Provisioning the EC2 infrastructure itself may be performed by Terraform or another approved method. The two responsibilities should be documented.

**Cost and safety:** EC2 instances, storage, IP addresses and data transfer may incur charges. Use a budget, least privilege, restricted security groups and a cleanup plan. Do not commit AWS credentials or private keys. A local-only project can practise the Ansible concepts without claiming that an AWS deployment was completed.

## 14. Testing and troubleshooting playbooks

Before execution:

```bash
ansible-inventory -i inventory.ini --graph
ansible-playbook -i inventory.ini site.yml --syntax-check
ansible-playbook -i inventory.ini site.yml --list-hosts
ansible-playbook -i inventory.ini site.yml --list-tasks
```

Common problems:

| Symptom | Checks |
|---|---|
| Host unreachable | Inventory hostname, DNS, route, firewall, SSH access |
| Permission denied | User, SSH key, `become`, sudo policy |
| Module not found | Ansible version and collection/module name |
| YAML parse error | Indentation, quoting, list structure |
| Task changes every run | Prefer idempotent module; inspect command/shell usage |
| Handler does not run | Confirm a changed task notified the correct handler |
| Wrong variable value | Inspect variable sources and precedence |
| API check fails | Verify URL, port, network path and application state |

## 15. Quick revision questions

1. What is an Ansible role? Explain its directory structure.
2. Write common `ansible-playbook` command options.
3. Explain the structure of a playbook.
4. What is a handler, and when does it execute?
5. How are environment variables supplied to a task?
6. What are Ansible variables and facts?
7. Explain `vars_prompt` and one limitation.
8. What are tags? Give example commands.
9. Explain blocks, rescue and always.
10. Describe a secure high-level Ansible deployment to AWS EC2.

## 16. Practical connection

This unit supports practicals 12–13 and 15: advanced playbooks, package/service automation, AWS application deployment, and Terraform-to-Ansible orchestration.

---

**Syllabus coverage:** Roles and CLI; playbook structure and execution; real-world examples; handlers; environment variables; variables; facts; prompts; tags; blocks; Ansible with AWS for application deployment.
