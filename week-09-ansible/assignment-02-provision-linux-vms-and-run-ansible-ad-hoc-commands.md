# Assignment 02 — Provision Linux VMs with Terraform and Run Ansible Ad-Hoc Commands

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will use Terraform to provision three or four Ubuntu Linux Virtual Machines on either Microsoft Azure or Amazon Web Services.

You will configure SSH key-based authentication, organize the servers using a custom Ansible inventory, and run Ansible ad-hoc commands across individual hosts and inventory groups.

---

# Task 1 — Create the Multi-Host Lab Structure

## Goal

Create a separate project directory for the multi-host lab and prepare the Terraform, Ansible, and documentation files.

This project will use the Git repository and Ansible controller prepared in Assignment 01.

### Evidence

#### Screenshot 1 — Terminal showing the complete `ansible-adhoc-lab` project structure

[Terminal showing the complete `ansible-adhoc-lab`](screenshots/Ass2-01.png)
---

#### Screenshot 2 — Terminal showing `git status --short` with the new project files and updated `.gitignore`

[Terminal showing `git status --short`](screenshots/Ass2-02.png)
---

### Notes

Task 1 — Project Structure and Git Setup

I created a team-ready project structure for the Terraform and Ansible lab.

The project contains separate directories for infrastructure provisioning and configuration management:

terraform/ — contains the Terraform configuration files used to provision the Azure infrastructure.
ansible/ — contains the Ansible configuration and inventory.
README.md — documents the project, architecture, commands, and assignment questions.
.gitignore — prevents sensitive files and local environment files from being committed to Git.

The project was initialized as a Git repository and the files were committed to the main branch.

I also configured the GitHub repository so the project could be version controlled and shared.
---

# Task 2 — Create the Terraform Configuration

## Goal

Create the Terraform configuration required to provision three or four Ubuntu Linux VMs on your selected cloud platform.

Complete only one option:

- Option A — Microsoft Azure
- Option B — Amazon Web Services

Do not configure both providers for this assignment.

### Evidence

#### Screenshot 3 — Terraform configuration showing the three or four server roles and the `for_each` or `count` implementation

[Terraform configuration showing the three or four server roles](screenshots/Ass2-03.png)
---

#### Screenshot 4 — Terraform configuration showing SSH restricted to the controller IP and HTTP allowed only for web hosts

[Terraform configuration showing SSH restricted to the controller IP](screenshots/Ass2-04.png)
---

#### Screenshot 5 — Terraform output configuration showing how public IP addresses are associated with the server roles

[Terraform output configuration showing how public IP addresses](screenshots/Ass2-05.png)
---

### Notes

Task 2 — Terraform Configuration

I created the Terraform configuration required to provision four Ubuntu Linux virtual machines in Azure.

The four servers were assigned different roles:

web1 — web server
web2 — web server
app1 — application server
db1 — database server

I used Terraform for_each with a role map so that the VM resources could be created without duplicating the same resource block for every server.

The Azure infrastructure included:

Resource group
Virtual network
Subnet
Network security groups
Network interfaces
Public IP addresses for the web servers
Linux virtual machines

The lab used the West US 2 Azure region and the Standard_F1ams_v7 VM size.

The Terraform configuration also disabled password-based SSH authentication and configured SSH public-key authentication.
---

# Task 3 — Provision the Infrastructure with Terraform

## Goal

Initialize and validate the Terraform configuration, review the execution plan, provision the selected three or four VMs, and retrieve their public IP addresses.

### Evidence

#### Screenshot 6 — Final `terraform apply` output showing `Apply complete`

[Final `terraform apply` output showing `Apply complete`](screenshots/Ass2-06.png)
---

#### Screenshot 7 — `terraform output public_ips` showing the role-to-IP mapping for all three or four VMs

[`terraform output public_ips` showing the role-to-IP mapping](screenshots/Ass2-07.png)
---

#### Screenshot 8 — Azure Portal or AWS Management Console showing all three or four VMs in the `Running` state, with their role-based names visible

[Azure Portal or AWS Management Console showing all three or four VMs](screenshots/Ass2-08.png)
---

### Notes

Task 3 — Azure Infrastructure Deployment

I used Terraform to initialize, validate, plan, and apply the infrastructure configuration.

The deployment created four Ubuntu Linux VMs with role-based names.

Public IP addresses were assigned to the two web servers, while the application and database servers remained on private IP addresses.

The final role-to-IP configuration was:

web1 — public IP
web2 — public IP
app1 — private IP
db1 — private IP

Terraform outputs were used to display the public and private IP addresses and the VM role mapping.

I also verified that the Azure resources were successfully created through the Azure environment.
---

# Task 4 — Verify SSH Key-Based Access

## Goal

Verify that each managed VM can be accessed from the Ansible controller using SSH key-based authentication.

### Evidence

#### Screenshot 9 — Terminal showing successful SSH hostname output from all VMs

[Terminal showing successful SSH hostname output from all VMs](screenshots/Ass2-09.png)
---

### Notes

Task 4 — SSH Connectivity

I verified SSH connectivity to all four Linux servers.

The web servers could be accessed directly through their public IP addresses.

The application and database servers did not have public IP addresses, so I accessed them through the web server using SSH ProxyCommand.

I verified the connection to each server by running the hostname command.

The expected hostname results were:

web1
web2
app1
db1

This confirmed that the SSH configuration and network connectivity were working correctly.
---

# Task 5 — Create the Custom Ansible Inventory

## Goal

Create an Ansible inventory file that groups the managed VMs by role.

The inventory allows Ansible to run commands against all servers, or only specific groups such as `web`, `app`, or `db`.

### Evidence

#### Screenshot 10 — `inventory.ini` showing the `web`, `app`, and `db` groups

[`inventory.ini` showing the `web`, `app`, and `db` groups](screenshots/Ass2-10.png)
---

#### Screenshot 11 — Output of `ansible-inventory -i inventory.ini --graph`

[Output of `ansible-inventory -i inventory.ini --graph`](screenshots/Ass2-11.png)
---

### Notes

Task 5 — Ansible Inventory

I created an Ansible inventory containing three logical groups:

[web] — web1 and web2
[app] — app1
[db] — db1

The web servers use their public IP addresses.

The application and database servers use their private IP addresses and connect through the web server using an SSH ProxyCommand.

The inventory also defines the common Ansible SSH user and private key.

I verified the inventory structure using:

ansible-inventory -i inventory.ini --graph

This confirmed that Ansible recognized the servers and their respective groups.
---

# Task 6 — Run Ansible Ad-Hoc Commands

## Goal

Run Ansible ad-hoc commands from the controller to verify connectivity, check server information, and manage packages and services across inventory groups.

This task proves that the inventory is working and that Ansible can control multiple managed VMs without writing a playbook.

### Evidence

#### Screenshot 12 — Output of `ansible all -i inventory.ini -m ping`

[Output of `ansible all -i inventory.ini -m ping`](screenshots/Ass2-12.png)
---

#### Screenshot 13 — Output of `ansible all -i inventory.ini -m command -a "uptime"`

[Output of `ansible all -i inventory.ini -m command -a "uptime"`](screenshots/Ass2-13.png)
---

#### Screenshot 14 — Output of `ansible web -i inventory.ini -m apt -a "name=nginx state=present update_cache=yes" --become`

[Output of `ansible web -i inventory.ini -m apt -a](screenshots/Ass2-14.png)
---

#### Screenshot 15 — Output of `ansible web -i inventory.ini -m service -a "name=nginx state=started enabled=yes" --become`

[Output of `ansible web -i inventory.ini -m service -a ](screenshots/Ass2-15.png)
---

#### Screenshot 16 — Output of `ansible all -i inventory.ini -m apt -a "name=htop state=present update_cache=yes" --become`

[Output of `ansible all -i inventory.ini -m apt](screenshots/Ass2-16.png)
---

#### Screenshot 17 — Output of `ansible web -i inventory.ini -m command -a "systemctl is-active nginx"`

[Output of `ansible web -i inventory.ini -m command -a](screenshots/Ass2-17.png)
---

### Notes

Task 6 — Ansible Ad-Hoc Commands

I used Ansible ad-hoc commands to test connectivity and perform basic configuration management on the servers.

First, I used the ping module to verify that Ansible could successfully communicate with all four servers.

Next, I used the command module to retrieve system uptime from all servers.

I then installed Nginx on the web servers using the Ansible apt module with privilege escalation.

After installation, I used the service module to:

Start Nginx
Enable Nginx to start automatically
Confirm that the service was running

I also installed htop on all four servers using Ansible.

Finally, I verified the Nginx service status on the web servers using:

systemctl is-active nginx

Both web servers returned active.

The ad-hoc commands demonstrated that Ansible could perform remote administration tasks across different server groups from a single controller environment.
---

# LinkedIn Post Required

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://lnkd.in/p/eqtApUt8
---

#### Screenshot — Published LinkedIn post

[Published LinkedIn post](screenshots/Ass2-18.png)
---

# Assignment Questions

Answer the following in your own words:

**1. What is the purpose of an Ansible inventory file?**

An Ansible inventory provides a structured list of the servers Ansible manages. It allows servers to be grouped according to their roles, such as web, application, and database servers. This makes it possible to target a group of machines instead of specifying individual IP addresses for every command.
---

**2. What is the difference between the `web`, `app`, and `db` groups in your inventory?**

The web group contains web1 and web2, which are the servers responsible for web traffic and have public IP addresses.

The app group contains app1, which represents the application server and is kept on the private network.

The db group contains db1, which represents the database server and is also kept on the private network.
---

**3. What does the Ansible `ping` module verify?**

The Ansible ping module verifies that Ansible can connect to the target server using SSH, authenticate successfully, execute the module, and communicate with the remote Python environment. A successful result returns pong.

It does not perform an ICMP network ping like the traditional Linux ping command.
---

**4. Why do package installation commands require `--become`?**

Installing packages and managing system services normally require administrator privileges on Linux. The --become option allows Ansible to execute the operation with elevated privileges, normally through sudo.
---

**5. When would you use an ad-hoc command instead of a playbook?**

An ad-hoc command is useful for quick, one-off administrative tasks such as checking uptime, testing connectivity, installing a package, or checking whether a service is running.

A playbook is more appropriate when the task involves multiple steps, needs to be repeated, or should be stored as version-controlled and repeatable automation.
---

**6. What is one challenge you faced while setting up SSH or inventory, and how did you fix it?**

The application and database servers did not have public IP addresses, so they could not be accessed directly from the controller. I configured SSH ProxyCommand to use web1 as a jump host so that Ansible could reach the private servers through the Azure virtual network.

I also had to make the SSH private key available inside my WSL environment because Ansible was running from WSL while the original key was stored in the Windows SSH directory. After copying the key into WSL and setting the appropriate permissions, Ansible was able to authenticate successfully.
---

# Required Files

Confirm that the following files are included in your assignment workspace:

- [ ] `ansible-adhoc-lab/README.md`
- [ ] `ansible-adhoc-lab/terraform/providers.tf`
- [ ] `ansible-adhoc-lab/terraform/main.tf`
- [ ] `ansible-adhoc-lab/terraform/variables.tf`
- [ ] `ansible-adhoc-lab/terraform/outputs.tf`
- [ ] `ansible-adhoc-lab/ansible/inventory.ini`
- [ ] Updated `.gitignore`

---

# Submission Instructions

- Add all required screenshots from the tasks.
- Full Name must be visible in required screenshots.
- Mention whether you used Azure or AWS.
- Mention whether you used the three-VM option or four-VM option.
- Add the public IP addresses of the VMs, redacted if preferred.
- Add your `inventory.ini` proof.
- Add a short explanation of what you learned.
- Answer all assignment questions clearly in your own words.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, Terraform state files, cloud credentials, passwords, access keys, secret keys, account IDs, or subscription IDs.
- Submit only one Google Doc link.

---

# Completion Checklist

- [ ] Task 1: `ansible-adhoc-lab` project structure created
- [ ] Task 1: `.gitignore` updated for Terraform files
- [ ] Task 2: Terraform configuration created
- [ ] Task 2: Server roles defined for either three or four VMs
- [ ] Task 2: `count` or `for_each` used
- [ ] Task 2: SSH restricted to the controller public IP
- [ ] Task 2: HTTP allowed only for web hosts
- [ ] Task 2: Terraform output maps roles to public IPs
- [ ] Task 3: Terraform initialized successfully
- [ ] Task 3: Terraform configuration validated
- [ ] Task 3: Terraform apply completed successfully
- [ ] Task 3: All selected VMs are running
- [ ] Task 4: SSH key-based access works for every VM
- [ ] Task 5: `inventory.ini` contains `web`, `app`, and `db` groups
- [ ] Task 5: `ansible-inventory -i inventory.ini --graph` shows the correct groups
- [ ] Task 6: `ansible all -i inventory.ini -m ping` returns `SUCCESS`
- [ ] Task 6: Ad-hoc commands run successfully
- [ ] Task 6: `--become` was used for package and service tasks
- [ ] Task 6: Nginx is active on the `web` group
- [ ] Screenshots 1–17 are included
- [ ] Assignment questions are answered
- [ ] LinkedIn post published
- [ ] LinkedIn post URL added
- [ ] No sensitive information is exposed
- [ ] Google Doc is accessible

---

## About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra and The CloudAdvisory, focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## Resources

- DMI Official Website: [https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme)
- University: [https://university.pravinmishra.com?utm_source=github&utm_medium=readme](https://university.pravinmishra.com?utm_source=github&utm_medium=readme)
- Discord Community: [https://discord.pravinmishra.com?utm_source=github&utm_medium=readme](https://discord.pravinmishra.com?utm_source=github&utm_medium=readme)
- Blog: [https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme)
- YouTube Playlist: [https://www.youtube.com/playlist?list=PLFeSNDtI4Cho](https://www.youtube.com/playlist?list=PLFeSNDtI4Cho)
- Pravin Mishra LinkedIn: [https://www.linkedin.com/in/pravin-mishra-aws-trainer/](https://www.linkedin.com/in/pravin-mishra-aws-trainer/)
- CloudAdvisory LinkedIn: [https://www.linkedin.com/company/thecloudadvisory/](https://www.linkedin.com/company/thecloudadvisory/)

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*