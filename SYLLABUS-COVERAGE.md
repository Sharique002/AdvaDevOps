# INT333 Syllabus Coverage Map

This map traces the supplied Session 2026–27 syllabus headings to the notes in this repository.

| Official unit | Syllabus topic groups | Notes file |
|---|---|---|
| Unit I | Configuration management; push/pull; Puppet introduction, rationale and components; Master, manifests, templates, files, CA; Agent, Facter; installation; isolated development | [Unit I](chapters/01-puppet-basics.md) |
| Unit II | Puppet configuration; packages; modules; web servers; load balancing; scaling; Master–Agent; dynamic configuration; extensions; classes/functions; CLI; `puppet apply`; manifests | [Unit II](chapters/02-advanced-puppet.md) |
| Unit III | Continuous monitoring; Nagios features/architecture/plugins/states; installation and prerequisites; web server/CLI/configuration; web-server monitoring; web interface; hosts/services/downtime/comments; sample app | [Unit III](chapters/03-nagios-monitoring.md) |
| Unit IV | Terraform; benefits/use cases; architecture/workflow; installation; configuration files; AWS/Azure/GCP; resources/dependencies; state; modules/workspaces; Terraform + Ansible; best practices/security | [Unit IV](chapters/04-terraform-iac.md) |
| Unit V | Ansible and configuration management; how it works; modern infrastructure; Red Hat; architecture; shell scripts to Ansible; installation; inventory; Vagrant | [Unit V](chapters/05-introduction-to-ansible.md) |
| Unit VI | Roles/CLI; playbooks; real-world examples; handlers; environment variables; variables; facts; prompts; tags; blocks; AWS deployment | [Unit VI](chapters/06-advanced-ansible.md) |

## Practical experiment coverage

All fifteen practicals listed in the syllabus are covered in [practicals/experiments.md](practicals/experiments.md):

1. Virtualization platform and learning VMs
2. Puppet installation and initial setup
3. Puppet manifests
4. Puppet modules
5. Puppet Master–Agent
6. Dynamic Puppet resource management
7. Nagios Core installation
8. Custom Nagios plugins
9. Web-server monitoring
10. Nagios web interface
11. Ansible installation and inventory
12. Ansible playbooks
13. Ansible application deployment with AWS
14. Terraform installation/configuration
15. Terraform with Ansible end-to-end automation

**Scope note:** the syllabus specifies the topics and experiment titles, not every command or version. Practical procedures in this repository are study guidance and must be adjusted to the instructor's lab environment.
