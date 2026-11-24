# INT333 — Practical Experiments (1–15)

> **Course:** DevOps Advance Configuration Management<br>
> **Session:** 2026–27<br>
> **Purpose:** Lab-oriented companion to the official INT333 syllabus.

The syllabus lists 15 experiments. The procedures below are study/lab guides, not proof that a particular experiment has been completed. Follow your instructor's VM images and environment requirements. Prefer disposable VMs or local test containers. Commands that install packages, change services, create cloud resources or remove infrastructure must be reviewed before execution.

## Lab safety checklist

- Take a VM snapshot before major system changes.
- Record OS and tool versions.
- Avoid running unknown scripts as root.
- Keep passwords, tokens and private keys out of Git.
- For cloud practicals, set a budget and use least-privilege credentials.
- Inspect Terraform plans before applying them.
- Do not run destructive commands against a personal or production environment.
- Capture genuine command output and screenshots for the lab record.

---

## Experiment 1 — Install and Configure Virtualization Platform

**Syllabus task:** Download and set up Oracle VirtualBox; import Puppet and Ansible learning VMs.

**Objective:** Prepare an isolated virtualization environment for configuration-management practice.

**Concepts:** Hypervisor, host OS, guest OS, virtual CPU, virtual memory, virtual disk, virtual networking, VM image/appliance.

**Procedure:**
1. Check host CPU virtualization support and available RAM/disk.
2. Obtain VirtualBox and the approved VM images from the instructor or trusted official source.
3. Verify downloads where checksums are provided.
4. Import the supplied appliance/image using the documented process.
5. Configure a suitable number of virtual CPUs, memory and disk within host capacity.
6. Select the network mode required by the lab (NAT, bridged, host-only or internal network).
7. Boot each VM and verify login, hostname, IP address and internet/lab connectivity.
8. Take a clean baseline snapshot.

**Verification:** VirtualBox reports the VM running; guest OS boots; guest network works as intended; snapshot is available.

**Evidence:** VirtualBox version, VM list, guest OS version, network configuration and successful boot screenshot.

## Experiment 2 — Puppet Installation and Initial Setup

**Syllabus task:** Install Puppet on a virtual machine and perform basic configuration tasks.

**Objective:** Install Puppet and verify that the command-line tool runs.

**Procedure:**
1. Inspect guest OS release and package sources.
2. Follow the version-compatible Puppet installation instructions approved for that OS.
3. Verify `puppet --version` and `puppet help`.
4. Inspect configuration with `puppet config print`.
5. Create a small test manifest in a disposable VM.
6. Run parser validation and inspect a no-op plan.

**Example checks:**

```bash
cat /etc/os-release
puppet --version
puppet config print
puppet help
```

**Verification:** Puppet executable is available; version is recorded; initial manifest validates.

**Evidence:** Installation output, version, configuration inspection and validation output.

## Experiment 3 — Develop and Apply Puppet Manifests

**Syllabus task:** Create and apply basic Puppet manifests to manage system resources.

**Objective:** Manage a directory and file using declarative desired state.

**Example manifest:**

```puppet
file { '/tmp/int333-lab':
  ensure => directory,
  mode   => '0755',
}

file { '/tmp/int333-lab/message.txt':
  ensure  => file,
  content => "Managed by Puppet\\n",
  mode    => '0644',
  require => File['/tmp/int333-lab'],
}
```

**Procedure:**
1. Save the manifest as `lab.pp`.
2. Run `puppet parser validate lab.pp`.
3. Preview with `puppet apply --noop lab.pp`.
4. Apply the manifest in the lab VM.
5. Inspect file content and permissions.
6. Apply it again and observe whether additional changes are required.

**Verification:** Desired directory/file exists; permissions/content match; repeat run is idempotent.

**Evidence:** Manifest, parser output, apply output, `ls -l` and file content.

## Experiment 4 — Puppet Module Creation and Deployment

**Syllabus task:** Design custom Puppet modules and deploy them in a simulated environment.

**Objective:** Convert related Puppet resources into a reusable module.

**Suggested layout:**

```text
modules/lab_web/
├── manifests/init.pp
├── files/
├── templates/
└── metadata.json
```

**Procedure:**
1. Create a module directory using the standard module structure.
2. Add a class in `manifests/init.pp`.
3. Add a static file or template if required.
4. Declare the class from a site manifest.
5. Validate syntax and module path.
6. Apply on a disposable target.
7. Repeat the run and verify desired state.

**Verification:** Puppet recognizes the module; class compiles; resources converge as expected.

**Evidence:** Module tree, class manifest, validation and apply output.

## Experiment 5 — Setup Puppet Master–Agent Architecture

**Syllabus task:** Configure Puppet Master and Agent, and test communication and certificate exchange.

**Objective:** Establish trusted communication between a Puppet Server and a managed agent.

**Procedure:**
1. Prepare two VMs or two isolated lab hosts with supported Puppet components.
2. Configure stable hostnames and name resolution.
3. Configure the agent's Puppet server setting.
4. Verify network connectivity and server availability.
5. Run the agent to submit a certificate request.
6. On the CA/server, inspect the certificate request and verify the node identity.
7. Sign only the intended certificate.
8. Run the agent again and inspect catalog application.

**Verification:** Certificate is signed; agent can retrieve/apply a catalog; server receives a report.

**Evidence:** Hostname resolution, agent run, pending request, certificate signing and successful agent report. Redact sensitive certificate/private-key material.

## Experiment 6 — Dynamic Resource Management Using Puppet

**Syllabus task:** Use facts and templates in Puppet to manage dynamic configurations.

**Objective:** Generate configuration based on parameters or node facts.

**Procedure:**
1. Inspect available facts with `facter`.
2. Create a Puppet class with parameters.
3. Create an EPP or approved template with dynamic values.
4. Render a configuration file.
5. Validate the manifest and template.
6. Apply on the lab VM.
7. Change a safe parameter and verify the generated output changes.

**Verification:** Output reflects supplied parameter/fact values; invalid values are rejected or handled safely.

**Evidence:** Fact output, class, template, rendered file and apply report.

## Experiment 7 — Install and Configure Nagios Core

**Syllabus task:** Install Nagios on CentOS/Ubuntu and configure basic host and service monitoring.

**Objective:** Install Nagios Core and monitor a basic target.

**Procedure:**
1. Identify the OS and use a compatible package or source installation guide.
2. Install required prerequisites and plugins.
3. Configure the Nagios service account and web-server interface as required.
4. Configure a host and a basic service check.
5. Validate Nagios configuration before starting/reloading the service.
6. Start Nagios and access the authenticated web interface.
7. Inspect host and service states.

**Verification:** Core starts; configuration validates; a host and service appear in the interface with a meaningful state.

**Evidence:** Version, configuration validation, service status and redacted dashboard screenshot.

## Experiment 8 — Create Custom Nagios Plugins

**Syllabus task:** Write and integrate basic Bash or Python plugins with Nagios.

**Objective:** Implement a small check that returns Nagios-compatible exit codes.

**Plugin contract:**

| Exit code | State |
|---:|---|
| 0 | OK |
| 1 | WARNING |
| 2 | CRITICAL |
| 3 | UNKNOWN |

**Procedure:**
1. Choose one measurable condition, such as disk usage or an HTTP status.
2. Define input arguments and thresholds.
3. Validate inputs and use a bounded timeout for external checks.
4. Print a concise status line.
5. Return the correct exit code.
6. Test OK, WARNING, CRITICAL and UNKNOWN/error paths.
7. Add the plugin command to Nagios configuration and validate.

**Verification:** Plugin returns the expected code and readable output for each test case; Nagios displays the resulting state.

**Evidence:** Plugin source, executable permissions, test-case output and Nagios service result.

## Experiment 9 — Monitor a Web Server with Nagios

**Syllabus task:** Configure Nagios to monitor Apache/Nginx web-server status, services and logs.

**Objective:** Monitor web availability and one additional relevant service/log condition.

**Procedure:**
1. Deploy or identify an Apache/Nginx web server in the lab.
2. Verify the service is running and the expected URL responds.
3. Identify the correct HTTP plugin and its path on the Nagios host/container.
4. Define a Nagios command and service check.
5. Set a reasonable check interval, retry interval and thresholds.
6. Test a healthy state.
7. Stop the web service in a disposable lab and verify failure detection.
8. Restart it and verify recovery.
9. Add log monitoring only with an appropriate plugin and clear matching criteria.

**Verification:** Nagios transitions through the expected state changes; check output identifies the cause.

**Evidence:** Healthy check, controlled failure, recovery, and relevant logs.

## Experiment 10 — Use Nagios Web Interface for System Monitoring

**Syllabus task:** Manage hosts, services, comments and view alerts through the web interface.

**Objective:** Use Nagios web UI as an operator.

**Procedure:**
1. Sign in to the Nagios web interface.
2. Inspect host and service status.
3. Open a service detail page and read check output/history.
4. Add a comment to a test service/host where supported.
5. Schedule downtime for a planned maintenance window.
6. Observe state and downtime information.
7. Remove or allow the scheduled downtime to expire according to the lab plan.

**Verification:** The interface displays the correct host/service information and records the configured operational actions.

**Evidence:** Screenshots of status, service details, comments and downtime. Do not expose passwords.

## Experiment 11 — Install and Configure Ansible

**Syllabus task:** Set up Ansible, create inventory files and use basic ad-hoc commands.

**Objective:** Install Ansible on a control node and verify a target connection.

**Procedure:**
1. Check OS and Python version.
2. Install Ansible using the approved distribution or isolated Python environment.
3. Verify `ansible --version`.
4. Create an INI inventory with a lab host or localhost.
5. Inspect inventory using `ansible-inventory`.
6. Run the Ansible `ping` module against the target.
7. Use a safe ad-hoc command to gather facts.

**Example localhost inventory:**

```ini
[local]
localhost ansible_connection=local
```

**Example commands:**

```bash
ansible-inventory -i hosts.ini --graph
ansible -i hosts.ini local -m ping
ansible -i hosts.ini local -m setup -a 'filter=ansible_distribution*'
```

**Verification:** Inventory parses and the selected connection works.

**Evidence:** Ansible version, inventory graph, ping result and fact output.

## Experiment 12 — Write and Execute Ansible Playbooks

**Syllabus task:** Create playbooks to automate tasks such as package installation and service configuration.

**Objective:** Automate a repeatable configuration task with a YAML playbook.

**Procedure:**
1. Create a playbook with a name, target hosts and tasks.
2. Use Ansible modules such as `package`, `file`, `copy` and `service` as appropriate.
3. Add variables and handlers if useful.
4. Run syntax check.
5. Inspect target hosts and tasks.
6. Execute in the lab.
7. Run a second time and examine idempotency.

**Example syntax check:**

```bash
ansible-playbook -i hosts.ini site.yml --syntax-check
```

**Verification:** YAML parses; tasks achieve the declared state; repeat run does not create unnecessary changes.

**Evidence:** Playbook, syntax-check output, execution output and target state.

## Experiment 13 — Deploy Applications Using Ansible with AWS

**Syllabus task:** Use Ansible to provision and deploy a sample web application on an AWS EC2 instance.

**Objective:** Configure and deploy a simple application on a test EC2 target using Ansible.

**Procedure:**
1. Obtain instructor approval and confirm a cloud budget.
2. Create or use an approved EC2 instance with a suitable OS and network configuration.
3. Use least-privilege AWS identity and an SSH key stored securely outside Git.
4. Restrict inbound security-group rules to required sources and ports.
5. Add the instance to Ansible inventory.
6. Verify SSH connection and Python support.
7. Write playbooks to install dependencies, deploy application files and configure/start the service.
8. Verify the application endpoint.
9. Record resource identifiers and costs where required.
10. Stop/terminate resources and remove temporary storage according to instructor guidance.

**Verification:** Ansible connects to the target; deployment tasks complete; HTTP test succeeds; cloud resources are cleaned up.

**Evidence:** Redacted inventory, playbook, execution output, HTTP result and resource cleanup evidence.

**Cost warning:** Cloud resources can incur charges even when the application is idle. Check the current pricing and billing console before and after the lab.

## Experiment 14 — Install and Configure Terraform

**Syllabus task:** Set up Terraform and write basic configuration files to provision cloud infrastructure.

**Objective:** Install Terraform, initialize a project and safely plan/apply a basic resource configuration.

**Procedure:**
1. Install a supported Terraform CLI release.
2. Verify `terraform version`.
3. Create provider, resource, variables and outputs configuration.
4. Initialize with `terraform init`.
5. Format with `terraform fmt`.
6. Validate with `terraform validate`.
7. Review `terraform plan`.
8. Apply only after checking credentials, resource impact and budget.
9. Verify the resource in the target environment.
10. Use the approved cleanup procedure when the exercise ends.

**Verification:** Init, formatting, validation and plan succeed; the resource is confirmed in the target environment if apply is part of the approved lab.

**Evidence:** Terraform version, source files, plan output and resource verification. Never commit state or secrets.

## Experiment 15 — Use Terraform with Ansible for End-to-End Automation

**Syllabus task:** Automate infrastructure provisioning with Terraform and configure it using Ansible.

**Objective:** Demonstrate the handoff from infrastructure provisioning to system configuration.

**Procedure:**
1. Define infrastructure resources in Terraform.
2. Initialize and validate the project.
3. Review the plan and apply approved changes.
4. Export required outputs such as host address and connection metadata.
5. Build or update Ansible inventory from the verified outputs.
6. Verify connectivity to the provisioned host.
7. Run Ansible playbooks to install/configure the application.
8. Test the application and record results.
9. Clean up only resources created for the experiment.

**Architecture:** Terraform creates infrastructure → outputs provide connection details → Ansible configures the host/application → tests verify service behavior.

**Verification:** Resource creation is successful; Ansible targets the correct host; configuration/deployment succeeds; service health is verified; cleanup is documented.

**Evidence:** Terraform plan/apply output, outputs, generated inventory (without secrets), Ansible run and service test.

---

## Suggested lab-record format

For each experiment, maintain these headings:

1. Experiment number and title.
2. Aim/objective.
3. Requirements and environment.
4. Theory/concepts.
5. Procedure/commands.
6. Configuration or source code.
7. Output/screenshots.
8. Result and learning.
9. Precautions and troubleshooting.

## Syllabus experiment checklist

- [ ] 1. Virtualization platform and learning VMs
- [ ] 2. Puppet installation and initial setup
- [ ] 3. Puppet manifests
- [ ] 4. Puppet modules
- [ ] 5. Puppet Master–Agent
- [ ] 6. Dynamic Puppet resources
- [ ] 7. Nagios Core
- [ ] 8. Custom Nagios plugins
- [ ] 9. Web-server monitoring
- [ ] 10. Nagios web interface
- [ ] 11. Ansible installation and inventory
- [ ] 12. Ansible playbooks
- [ ] 13. Ansible with AWS
- [ ] 14. Terraform
- [ ] 15. Terraform + Ansible
