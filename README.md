# INT333 — DevOps Advance Configuration Management

Syllabus-aligned study notes for **Session 2026–27**.

This repository organizes the course into six chapter-wise Markdown files and a practical-experiments guide. The unit structure and listed topics follow the uploaded INT333 syllabus. Explanations, examples and suggested lab steps expand those headings for study; they are not additional official syllabus requirements.

## Quick links

- [Syllabus coverage map](SYLLABUS-COVERAGE.md)
- [Practical experiments 1–15](practicals/experiments.md)
- [Unit I — Puppet Basics](chapters/01-puppet-basics.md)
- [Unit II — Advanced Puppet](chapters/02-advanced-puppet.md)
- [Unit III — Nagios Monitoring](chapters/03-nagios-monitoring.md)
- [Unit IV — Terraform](chapters/04-terraform-iac.md)
- [Unit V — Introduction to Ansible](chapters/05-introduction-to-ansible.md)
- [Unit VI — Advanced Ansible](chapters/06-advanced-ansible.md)

## Repository layout

```text
.
├── README.md
├── SYLLABUS-COVERAGE.md
├── chapters/
│   ├── 01-puppet-basics.md
│   ├── 02-advanced-puppet.md
│   ├── 03-nagios-monitoring.md
│   ├── 04-terraform-iac.md
│   ├── 05-introduction-to-ansible.md
│   └── 06-advanced-ansible.md
└── practicals/
    └── experiments.md
```

## Course outcomes

| Outcome | Expected competency |
|---|---|
| CO1 | Understand the basics of Puppet |
| CO2 | Demonstrate configuration management skills using Puppet |
| CO3 | Install and configure Nagios for continuous monitoring |
| CO4 | Understand and implement infrastructure automation using Terraform |
| CO5 | Describe the fundamentals of Ansible and its installation process |
| CO6 | Construct Ansible playbooks to automate multiple system administration tasks |

## Chapter-wise notes

| Unit | Chapter | Notes |
|---|---|---|
| I | Puppet Basics | [01-puppet-basics.md](chapters/01-puppet-basics.md) |
| II | Advanced Puppet | [02-advanced-puppet.md](chapters/02-advanced-puppet.md) |
| III | Nagios Monitoring | [03-nagios-monitoring.md](chapters/03-nagios-monitoring.md) |
| IV | Infrastructure as Code with Terraform | [04-terraform-iac.md](chapters/04-terraform-iac.md) |
| V | Introduction to Ansible | [05-introduction-to-ansible.md](chapters/05-introduction-to-ansible.md) |
| VI | Advanced Ansible | [06-advanced-ansible.md](chapters/06-advanced-ansible.md) |

## Practical experiments

[Practical Experiments 1–15](practicals/experiments.md) — aims, concepts, procedure, verification and evidence suggestions for every experiment listed in the syllabus.

## Suggested study and lab workflow

1. Check the [coverage map](SYLLABUS-COVERAGE.md) to locate a syllabus topic.
2. Read the corresponding unit notes, then recreate key diagrams and workflows from memory.
3. Use the related practical as a checklist; adapt its procedure to the instructor-approved environment.
4. Practise in a disposable VM, container or approved lab, and capture your own command output and observations.
5. Verify the result, clean up lab resources, and answer the chapter's revision questions.

## Safety and environment notes

- Commands are illustrative and may need adjustment for the Linux distribution, package version, privileges and lab topology.
- Review any command that installs packages, changes services, modifies infrastructure or deletes resources before running it.
- Terraform examples require a compatible provider and working API access; do not apply a plan until its effects are understood.
- AWS/Azure/GCP practicals can incur charges. Use instructor-approved accounts, least-privilege credentials and a cleanup plan.
- Never commit credentials, private keys, `.env` secrets or Terraform state files.
- Record versions, configuration changes, verification results and cleanup in your lab record; do not present suggested output as if you executed it.

## Syllabus references

The structure follows the supplied **INT333: DevOps Advance Configuration Management**, Session 2026–27 syllabus. The syllabus lists these references:

1. *Mastering Puppet 5* — Ryan Russell-Yates, Packt Publishing.
2. *Ansible for DevOps: Server and Configuration Management for Humans* — Jeff Geerling.
3. *Learning Nagios* — Wojciech Kocjan.

These notes are a learning companion, not a replacement for prescribed textbooks, official product documentation, lab instructions or instructor guidance.
