# Assignment 04 — Deploy Mini Finance on Azure Using Terraform and Ansible

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will provision Azure infrastructure using Terraform and deploy the Mini Finance website using an Ansible multi-play playbook.

Terraform will create the Azure Virtual Machine and networking resources. Ansible will install Nginx, clone the Mini Finance repository, deploy the website, and verify the deployment.

---

# Task 1 — Create the Project Structure

## Goal

Create separate directories and files for the Terraform infrastructure and Ansible configuration.

### Evidence

#### Screenshot 1 — Terminal or VS Code showing the complete `mini-finance` project structure

[mini-finance` project structure](screenshots/Ass4-01.png)

---

### Notes

I created a structured project directory named `mini-finance`, with separate `terraform` and `ansible` directories to organize infrastructure provisioning and configuration management. I also created the required configuration files, README.md and .gitignore to support project organization, documentation and secure version control.
---

# Task 2 — Create the Azure Infrastructure Using Terraform

## Goal

Use Terraform to provision an Ubuntu Virtual Machine with the required Azure networking and security resources.

### Evidence

#### Screenshot 2 — Terraform code showing the `Allow-SSH` rule for port `22` and the `Allow-HTTP` rule for port `80`

[`Allow-SSH` rule for port `22` and the `Allow-HTTP` rule for port `80`](screenshots/Ass4-02.png)
---

#### Screenshot 3 — Terraform code showing the association between `nsg-mini-finance` and `nic-mini-finance`

[association between `nsg-mini-finance` and `nic-mini-finance`](screenshots/Ass1-03.png)
---

### Notes

I developed the Terraform configuration to provision the required Azure infrastructure. I configured the Network Security Group with separate inbound rules for SSH (port 22) and HTTP (port 80), ensuring administrative access was restricted to my public IP while allowing public access to the website. I also associated the Network Security Group with the virtual machine's network interface.
---

# Task 3 — Initialize and Apply the Terraform Configuration

## Goal

Format and validate the Terraform configuration, review the execution plan, and provision the Azure infrastructure.

### Evidence

#### Screenshot 4 — End of the `terraform apply` output showing `Apply complete!` with no errors

[`terraform apply` output showing `Apply complete!`](screenshots/Ass4-04.png)
---

#### Screenshot 5 — Output of `terraform output public_ip` showing the VM’s public IP address

[Output of `terraform output public_ip`](screenshots/Ass4-05.png)
---

### Notes

I formatted and initialized the Terraform configuration, validated the code, reviewed the execution plan and successfully applied the configuration. Terraform provisioned the required Azure resources and generated the virtual machine's public IP address, which was subsequently used for SSH connectivity, Ansible configuration and website deployment.
---

# Task 4 — Verify Passwordless SSH Access

## Goal

Confirm that the Ansible controller can connect to the Terraform-provisioned Azure VM using SSH key authentication.

### Evidence

#### Screenshot 6 — Passwordless SSH command and the returned `mini-finance` hostname

[Passwordless SSH command and the returned `mini-finance` hostname](screenshots/Ass4-06.png)
---

### Notes

I verified SSH connectivity between my Ansible control node and the Azure virtual machine using key-based authentication. The connection was successful without requiring a password, and the returned hostname confirmed that the target virtual machine was reachable and ready for configuration management.
---

# Task 5 — Create the Ansible Inventory and Verify Connectivity

## Goal

Add the Terraform-provisioned Azure VM to the Ansible inventory and confirm that Ansible can connect to it.

### Evidence

#### Screenshot 7 — Ansible ping output showing `SUCCESS` and `pong` from the Azure VM

[Ansible ping output showing `SUCCESS` and `pong` from the Azure VM](screenshots/Ass4-07.png)
---

### Configuration File

Copy and paste the complete contents of your `ansible/inventory.ini` file below:

```ini
[web]
mini-finance ansible_host=20.114.24.50 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/terraform-azure-vm-key

```

---

# Task 6 — Create the Multi-Play Ansible Playbook

## Goal

Create one Ansible playbook containing separate plays to install Nginx, deploy the Mini Finance website, and verify the deployment.

### Evidence

#### Screenshot 8 — `site.yml` showing Play 1 and the beginning of Play 2

Screenshot must show:

- Play 1 targeting the `web` group
- Installation of `nginx`, `git`, and `rsync`
- Nginx service configured as started and enabled
- Beginning of Play 2 with the Git repository URL and synchronization task

[`site.yml` showing Play 1 and the beginning of Play 2](screenshots/Ass4-08.png)
---

#### Screenshot 9 — `site.yml` showing the deployment destination, handler, and Play 3 verification

Screenshot must show:

- Website destination `/var/www/html/`
- Ownership set to `www-data:www-data`
- Nginx reload handler
- Play 3 targeting `localhost`
- The `uri` verification and `assert` condition

[`site.yml` showing the deployment destination, handler, and Play 3](screenshots/Ass4-09.png)
---

### Configuration File

Copy and paste the complete contents of your `ansible/site.yml` file below:

```yaml

---
# Play 1: Install and start the required web server packages
- name: Install and configure web server
  hosts: web
  become: true

  tasks:
    - name: Install Nginx, Git and rsync
      ansible.builtin.apt:
        name:
          - nginx
          - git
          - rsync
        state: present
        update_cache: true

    - name: Ensure Nginx is started and enabled
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true

# Play 2: Clone and deploy Mini Finance
- name: Deploy Mini Finance website
  hosts: web
  become: true

  tasks:
    - name: Clone Mini Finance repository
      ansible.builtin.git:
        repo: "https://github.com/pravinmishraaws/mini-finance-project.git"
        dest: /opt/mini-finance
        version: main
        force: true

    - name: Synchronize website files to Nginx web directory
      ansible.posix.synchronize:
        src: /opt/mini-finance/
        dest: /var/www/html/
        archive: true
        delete: true
      delegate_to: "{{ inventory_hostname }}"
      notify: Reload nginx

    - name: Set website directory ownership
      ansible.builtin.file:
        path: /var/www/html/
        owner: www-data
        group: www-data
        recurse: true
        state: directory

  handlers:
    - name: Reload nginx
      ansible.builtin.service:
        name: nginx
        state: reloaded

# Play 3: Verify website availability from the controller
- name: Verify Mini Finance website
  hosts: localhost
  connection: local
  gather_facts: false

  tasks:
    - name: Check website HTTP response
      ansible.builtin.uri:
        url: "http://20.114.24.50"
        method: GET
        status_code: 200
      register: website_response

    - name: Assert website is responding successfully
      ansible.builtin.assert:
        that:
          - website_response.status == 200
        success_msg: "Mini Finance website is accessible."
        fail_msg: "Mini Finance website verification failed."

```

---

# Task 7 — Validate and Run the Ansible Playbook

## Goal

Validate the syntax of the multi-play Ansible playbook and run it to install Nginx, deploy the Mini Finance website, and verify the deployment.

### Evidence

#### Screenshot 10 — Successful playbook syntax check showing `playbook: site.yml`

[Successful playbook syntax check showing `playbook: site.yml`](screenshots/Ass4-10.png)
---

#### Screenshot 11 — Play 3 output showing the successful HTTP verification and assertion

[Play 3 output showing the successful HTTP verification and assertion](screenshots/Ass4-11.png)
---

#### Screenshot 12 — Final `PLAY RECAP` showing `failed=0` and `unreachable=0`

[Final `PLAY RECAP` showing `failed=0` and `unreachable=0`](screenshots/Ass4-12.png)
---

### Notes

I validated the Ansible playbook syntax before execution and successfully ran the multi-play deployment workflow. The playbook installed and configured Nginx, deployed the Mini Finance website and performed automated HTTP verification. The deployment completed successfully, with the final play recap reporting zero failed and unreachable tasks.
---

# Task 8 — Test the Mini Finance Website in a Browser

## Goal

Confirm that the Mini Finance website is publicly accessible through the Azure VM’s public IP address.

### Evidence

#### Screenshot 13 — Mini Finance website successfully loading in the browser, with the Azure VM’s public IP address visible in the address bar

[Mini Finance website successfully loading in the browser](screenshots/Ass4-13.png)
---

### Website URL

Add your deployed website URL below:

```text
http://20.114.24.50/
```

---

# Task 9 — Create the Project README

## Goal

Create a `README.md` file to document the Mini Finance infrastructure and deployment project.

### Evidence

#### Screenshot 14 — Completed `README.md` displayed in the VS Code Markdown preview or terminal

[`README.md` displayed in the VS Code Markdown](screenshots/Ass4-14.png)
---

### README Content

Copy and paste the complete contents of your `README.md` file below:

```markdown
# Mini Finance – Azure Deployment with Terraform and Ansible

## Project Overview

This project demonstrates the deployment of a static Mini Finance website on Microsoft Azure using Infrastructure as Code (IaC) and configuration management.

Terraform provisions the Azure infrastructure, while Ansible configures the Linux virtual machine, installs Nginx and deploys the website from GitHub.

## Architecture

The deployment consists of:

- Azure Resource Group
- Azure Virtual Network and Subnet
- Network Security Group (NSG)
- Static Public IP
- Network Interface
- Ubuntu Linux Virtual Machine
- Nginx Web Server

**Deployment flow:**

1. Terraform provisions the Azure infrastructure.
2. Ansible connects to the VM through SSH.
3. Ansible installs Nginx, Git and rsync.
4. Ansible clones the Mini Finance repository on the controller.
5. Ansible synchronizes the website files to the VM's Nginx web directory.
6. Ansible verifies the website using an HTTP request.

## Technologies Used

- Microsoft Azure
- Terraform
- Ansible
- Ubuntu Linux
- Nginx
- Git and GitHub
- SSH
- YAML
- Bash

## Project Structure

```text
mini-finance/
├── .gitignore
├── README.md
├── terraform/
│   ├── providers.tf
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
└── ansible/
    ├── inventory.ini
    └── site.yml
```

## Infrastructure Provisioning with Terraform

Terraform was used to provision:

- Resource Group: `mini-finance-rg`
- Virtual Network: `vnet-mini-finance`
- Subnet: `subnet-mini-finance`
- Network Security Group: `nsg-mini-finance`
- Public IP: `pip-mini-finance`
- Network Interface: `nic-mini-finance`
- Ubuntu Virtual Machine: `mini-finance`

### Terraform commands

Run these commands from the project root:

```bash
cd terraform
terraform init
terraform validate
terraform plan
terraform apply
```

To display the VM public IP:

```bash
terraform output public_ip
```

## Ansible Configuration and Deployment

Ansible was used to automate the configuration and deployment process.

The playbook contains three plays:

**Play 1 – Install and Configure Web Server**

- Installs Nginx, Git and rsync.
- Starts Nginx and enables it to run on boot.

**Play 2 – Deploy Mini Finance**

- Clones the GitHub repository on the Ansible controller.
- Synchronizes website files to `/var/www/html/`.
- Sets ownership to `www-data:www-data`.
- Reloads Nginx when website files change.

**Play 3 – Verify Deployment**

- Sends an HTTP GET request to the website.
- Checks for HTTP status code `200`.
- Uses Ansible assert to confirm successful deployment.

### Ansible commands

Test SSH connectivity:

```bash
ansible web -i ansible/inventory.ini -m ansible.builtin.ping
```

Check playbook syntax:

```bash
ansible-playbook -i ansible/inventory.ini ansible/site.yml --syntax-check
```

Execute the deployment:

```bash
ansible-playbook -i ansible/inventory.ini ansible/site.yml
```

## Deployment Information

- Cloud provider: Microsoft Azure
- Region: West US 2
- Operating system: Ubuntu 24.04 LTS
- Web server: Nginx
- Public IP: `20.114.24.50`
- Website URL: http://20.114.24.50

## Security Considerations

- SSH authentication uses an SSH key pair.
- SSH access on port 22 is restricted to the configured public IP address.
- HTTP access on port 80 is publicly available for website visitors.
- Private SSH keys and Terraform state files are excluded from Git tracking.
- Credentials and sensitive information should never be committed to the repository.

## Deployment Verification

The deployment was successful.

The Ansible playbook completed all three plays, and the HTTP verification returned status code `200`.

Final Ansible recap:

```text
localhost:
ok=2 changed=0 unreachable=0 failed=0

mini-finance:
ok=8 changed=5 unreachable=0 failed=0
```

The Mini Finance website was also opened successfully in a web browser using the Azure VM public IP.

## Cleanup

To avoid unnecessary Azure charges after completing the assignment and capturing the required evidence, destroy the provisioned infrastructure:

```bash
cd terraform
terraform destroy
```

Confirm the destruction when prompted.

## Conclusion

This project provided practical experience combining Terraform and Ansible in an Azure deployment workflow.

It demonstrated how infrastructure provisioning, server configuration, website deployment and application verification can be automated rather than performed manually.

**Repository:** https://github.com/pravinmishraaws/mini_finance

```

---

# LinkedIn Post Required

## Evidence

#### Screenshot 15 — Published LinkedIn post showing the text and at least one deployment screenshot

[Published LinkedIn post showing the text and at least one deployment](screenshots/Ass4-15.png)
---

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://lnkd.in/p/eNDdYwcA
---

### LinkedIn Submission Notes

**One challenge you faced and how you fixed it:**

One challenge was adapting the deployment workflow to the static website's structure. I addressed this by inspecting the source repository and implementing an Ansible workflow that clones the application on the control node and synchronizes the website files to the Azure server using rsync.
---

**One real-world example where you can use this learning:**

This workflow can be applied when deploying web applications to cloud infrastructure in production environments. Terraform can provision the required infrastructure, while Ansible automates server configuration, application deployment and post-deployment verification. This approach supports repeatable deployments and reduces manual configuration errors.
---

# Assignment Questions

Answer the following in your own words:

**1. What did you provision using Terraform in this assignment?**

I used Terraform to provision the Azure infrastructure required to host the Mini Finance application. This included a Resource Group, Virtual Network, Subnet, Network Security Group, Network Interface, Public IP address and Ubuntu Virtual Machine. Terraform allowed me to define and provision these resources consistently through Infrastructure as Code.

---

**2. What did Ansible configure and deploy in this assignment?**

I used Ansible to automate the configuration of the Azure virtual machine and deployment of the Mini Finance website. The playbook installed Nginx, Git and rsync, ensured that the Nginx service was running and enabled, transferred the website files into `/var/www/html/`, and configured the appropriate directory ownership.

---

**3. Why is SSH access on port `22` restricted to your public IP address?**

SSH provides administrative access to the virtual machine. Restricting port 22 to my public IP address reduces exposure to unauthorized access attempts and limits the number of systems that can initiate SSH connections to the server.

---

**4. Why is HTTP port `80` open to the internet?**

Port 80 allows users to access the Mini Finance website through a web browser using HTTP. Since the application is intended to be publicly accessible, the port must accept inbound web traffic from the internet.
---

**5. What is the purpose of the Ansible inventory file?**

The Ansible inventory file defines the target machines that Ansible manages. In this assignment, it contained the Azure VM's connection details, including its IP address, SSH username and private key location. It enabled Ansible to identify and connect to the target server when executing the playbook.
---

**6. Why does the playbook use separate plays for install, deploy, and verify?**

Separating the playbook into different plays makes the automation easier to understand, maintain and troubleshoot. The first play handles server preparation and Nginx configuration, the second handles application deployment, while the third independently verifies that the application is accessible. This separation also makes each stage of the deployment process easier to manage.
---

**7. Why is `rsync` useful when deploying website files?**

Rsync efficiently transfers and synchronizes files between systems. It can transfer only files that have changed instead of repeatedly copying everything, reducing unnecessary data transfer and making deployments more efficient. In this assignment, Ansible's synchronize module used rsync to transfer the website files from the control node to the Azure VM.
---

**8. What does the Ansible `uri` module verify in this assignment?**

The Ansible `uri` module sends an HTTP request to the deployed website and checks the response. In this assignment, it verified that the Mini Finance website returned HTTP status code 200, indicating that the web server successfully responded to the request. An assertion then confirmed the expected result.
---

**9. What issue did you face during this assignment, and how did you fix it?**

One challenge was configuring the website deployment workflow because the application was a static website rather than a Node.js application. I inspected the source repository, identified the correct deployment approach, and adjusted the Ansible playbook to clone the repository on the control node and synchronize its files to the Azure VM using Ansible's synchronize module. I also ensured rsync was installed on the required systems.
---

**10. What did you learn from using Terraform and Ansible together?**

I learned how Terraform and Ansible complement each other in an automated infrastructure and deployment workflow. Terraform handles infrastructure provisioning, while Ansible manages server configuration and application deployment. Combining both tools creates a repeatable workflow that reduces manual intervention, improves consistency and makes infrastructure management more efficient.
---

# Required Files

Confirm that the following files are included in your assignment folder:

- [ ] `.gitignore`
- [ ] `README.md`
- [ ] `terraform/providers.tf`
- [ ] `terraform/main.tf`
- [ ] `terraform/variables.tf`
- [ ] `terraform/outputs.tf`
- [ ] `ansible/inventory.ini`
- [ ] `ansible/site.yml`

---

# Submission Instructions

- Add all required screenshots in the correct order.
- Full Name must be visible in required screenshots.
- Add the Azure VM public IP address.
- Add the final Mini Finance website URL.
- Paste `inventory.ini`, `site.yml`, and `README.md` as editable text.
- Answer all assignment questions clearly in your own words.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, passwords, Azure credentials, subscription IDs, Terraform state contents, or other sensitive information.

---

# Completion Checklist

- [ ] Task 1: `mini-finance` project structure created
- [ ] Task 1: `.gitignore` created
- [ ] Task 2: Terraform Azure infrastructure code created
- [ ] Task 2: `Allow-SSH` rule configured for port `22`
- [ ] Task 2: `Allow-HTTP` rule configured for port `80`
- [ ] Task 2: NSG associated with the Network Interface
- [ ] Task 3: `terraform fmt` completed
- [ ] Task 3: `terraform init` completed
- [ ] Task 3: `terraform validate` completed successfully
- [ ] Task 3: `terraform apply` completed successfully
- [ ] Task 3: `terraform output public_ip` displayed the VM public IP
- [ ] Task 4: Passwordless SSH works from the Ansible controller
- [ ] Task 5: `inventory.ini` created
- [ ] Task 5: Ansible ping returns `SUCCESS` and `pong`
- [ ] Task 6: `site.yml` contains three separate plays
- [ ] Task 6: Play 1 installs Nginx, Git, and rsync
- [ ] Task 6: Play 2 clones and deploys the Mini Finance website
- [ ] Task 6: Play 3 verifies HTTP status code `200`
- [ ] Task 7: Playbook syntax check passes
- [ ] Task 7: Ansible playbook completes successfully
- [ ] Task 7: Final recap shows `failed=0` and `unreachable=0`
- [ ] Task 8: Mini Finance website loads in the browser
- [ ] Task 8: Azure VM public IP is visible in the browser screenshot
- [ ] Task 9: `README.md` completed
- [ ] Screenshots 1–15 are included
- [ ] `inventory.ini`, `site.yml`, and `README.md` are pasted as editable text
- [ ] Assignment questions are answered
- [ ] LinkedIn post published with Anyone visibility
- [ ] LinkedIn post URL added
- [ ] No sensitive information is exposed

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

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track.*