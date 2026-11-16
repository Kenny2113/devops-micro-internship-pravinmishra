# Assignment — Deploy EpicBook with Terraform and Ansible Roles

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will deploy the EpicBook web application using Terraform and Ansible roles.

Terraform provisions the cloud infrastructure, including one Ubuntu VM and one managed MySQL database. Ansible roles configure the VM, install required software, deploy the EpicBook application, configure Nginx, connect the app to the managed MySQL database, and verify the deployment.

---

# Task 1 — Set Up the Project Folder Layout

## Goal

Create the project folder structure for Terraform and Ansible roles.

Terraform will be used to provision the cloud infrastructure. Ansible roles will be used to configure the VM and deploy the EpicBook application.

### Evidence

#### Screenshot 1 — Terminal showing the completed `epicbook-prod` project structure

[Terminal showing the completed `epicbook-prod` project structure](screenshots/Ass5-01.png)
---

### Notes

Answer the following in your own words:

**1. Which cloud provider did you choose for this assignment?**

I used Amazon Web Services (AWS) as the cloud provider for this deployment. The infrastructure was provisioned in the us-east-1 region.
---

**2. Why is it useful to keep Terraform files and Ansible files in separate folders?**

Separating Terraform and Ansible files establishes a clear distinction between infrastructure provisioning and server configuration. Terraform manages the creation and lifecycle of cloud resources, while Ansible handles software installation, server configuration, and application deployment. This structure improves maintainability, organization, and reusability.
---

**3. What is the purpose of the `roles` directory in Ansible?**

The roles directory contains reusable and organized units of Ansible automation. Each role manages a specific responsibility through its tasks, templates, handlers, and variables. This makes the deployment modular, easier to maintain, and more consistent.
---

# Task 2 — Provision the Infrastructure with Terraform

## Goal

Run Terraform to provision the cloud infrastructure for the EpicBook deployment.

Terraform will create the VM, managed MySQL database, networking, security rules, and required outputs.

### Evidence

#### Screenshot 2 — `terraform apply` completed successfully

[`terraform apply` completed successfully](screenshots/Ass5-02.png)
---

#### Screenshot 3 — Output of `terraform output`

[Output of `terraform output`](screenshots/Ass5-03.png)
---

#### Screenshot 4 — Azure Portal or AWS Console showing the VM running

[Azure Portal or AWS Console showing the VM running](screenshots/Ass5-04.png)
---

#### Screenshot 5 — Azure Portal or AWS Console showing the managed MySQL database created

[Azure Portal or AWS Console showing the managed MySQL database created](screenshots/Ass5-05.png)
---

### Notes

Answer the following in your own words:

**1. What resources did Terraform create for this assignment?**

Terraform provisioned the AWS infrastructure required for EpicBook, including a VPC, three subnets, an Internet Gateway, route table and associations, security groups, an EC2 key pair, a managed MySQL RDS database, a DB subnet group, and an Ubuntu EC2 instance. It also generated a database password and exposed the required infrastructure outputs.
---

**2. Why should you review `terraform plan` before running `terraform apply`?**

Reviewing terraform plan allows me to inspect the infrastructure changes Terraform intends to make before applying them. It helps identify incorrect configurations, unintended resource creation or deletion, and potential security issues. This reduces deployment errors and gives better control over infrastructure changes.
---

**3. Why should database passwords not be shown in Terraform output?**

Database passwords are sensitive credentials. Exposing them in Terraform output can lead to unauthorized access if the output is shared, logged, or committed to source control. Sensitive credentials should be protected using Terraform's sensitive output handling and secure secrets-management practices.
---

# Task 3 — Verify SSH Key-Based Access

## Goal

Verify that the cloud VM can be accessed from the Ansible controller using SSH key-based authentication.

### Evidence

#### Screenshot 6 — Successful SSH hostname check from the Ansible controller

[Successful SSH hostname check from the Ansible controller](screenshots/Ass5-06.png)
---

### Notes

Answer the following in your own words:

**1. What command did you use to verify SSH access?**

I used an SSH command with the VM's public IP address, the appropriate Linux username, and the corresponding private key:
ssh -i <path-to-private-key> <ssh-user>@100.54.23.195
---

**2. What proves that SSH key-based access worked successfully?**

Successful authentication into the Ubuntu EC2 instance and the ability to execute commands on the remote server confirmed that SSH key-based access was working. The server's hostname command also confirmed that I was connected to the intended VM.
---

**3. What would you check if SSH returned `Permission denied (publickey)`?**

I would verify that the correct private key was being used, confirm the SSH username, check the private key's file permissions, and ensure that the corresponding public key was registered on the EC2 instance. I would also confirm that the EC2 security group allows SSH traffic from the controller's public IP address.
---

# Task 4 — Create the Ansible Inventory and Configuration

## Goal

Create the Ansible inventory file and local Ansible configuration for the EpicBook VM.

The inventory tells Ansible which VM to manage and which SSH user to use.

### Evidence

#### Screenshot 7 — `inventory.ini` showing the VM under the `web` group

Add your screenshot here.

---

#### Screenshot 8 — Output of `ansible-inventory -i inventory.ini --graph`

[`inventory.ini` showing the VM under the `web` group](screenshots/Ass5-08.png)
---

#### Screenshot 9 — Output of `ansible web -i inventory.ini -m ping`

[Output of `ansible web -i inventory.ini -m ping`](screenshots/Ass5-09.png)
---

### Notes

Answer the following in your own words:

**1. What is the purpose of `inventory.ini`?**

The inventory file identifies the remote hosts Ansible manages and organizes them into groups. In this deployment, it defines the EpicBook EC2 instance under the web group and provides the connection details Ansible needs to communicate with it.
---

**2. What does `ansible_host` store?**

ansible_host stores the IP address or hostname Ansible uses to connect to a managed host. For this deployment, it contains the public IP address of the EpicBook EC2 instance.
---

**3. What does `ansible_ssh_private_key_file` tell Ansible?**

It specifies the path to the private SSH key Ansible should use when authenticating with the remote server. This allows Ansible to connect using key-based authentication without requiring an interactive password.
---

**4. Why is `host_key_checking = False` used only for this temporary lab?**

It disables SSH host-key verification, which can simplify connections when working with temporary lab instances whose host keys may change. However, it reduces protection against man-in-the-middle attacks. In a production environment, host-key verification should remain enabled and trusted host keys should be managed appropriately.
---

# Task 5 — Create the Main Ansible Playbook

## Goal

Create the main Ansible playbook that runs the required roles in the correct order.

The `site.yml` file will call the `common`, `nginx`, and `epicbook` roles.

### Evidence

#### Screenshot 10 — `site.yml` showing the roles in the correct order

[`site.yml` showing the roles in the correct order](screenshots/Ass5-10.png)
---

#### Screenshot 11 — Output of `ansible-playbook -i inventory.ini site.yml --syntax-check`

[Output of `ansible-playbook -i inventory.ini site.yml --syntax-check`](screenshots/Ass5-11.png)
---

### Notes

Answer the following in your own words:

**1. What is the purpose of `site.yml`?**

site.yml is the main Ansible playbook that coordinates the configuration and deployment of the EpicBook environment. It defines the target hosts, privilege escalation requirements, and the roles that must execute to prepare the server and deploy the application.
---

**2. Why should the roles run in the order `common`, `nginx`, and `epicbook`?**

The common role prepares the server with the required system packages and dependencies. The nginx role installs and configures the web server. The epicbook role then deploys and configures the application. This order ensures that the underlying server and web server are prepared before the application deployment is completed.
---

**3. What does `become: true` allow Ansible to do?**

become: true enables privilege escalation, allowing Ansible to execute tasks with elevated permissions, usually through sudo. This is necessary for operations such as installing packages, modifying system configurations, and managing services.
---

# Task 6 — Create the `common` Role

## Goal

Create the `common` role to prepare the Ubuntu VM with the basic packages required for the EpicBook deployment.

This role handles the common server setup before Nginx and the application are configured.

### Evidence

#### Screenshot 12 — `roles/common/tasks/main.yml` showing the common setup tasks

[`roles/common/tasks/main.yml` showing the common setup tasks](screenshots/Ass5-12.png)
---

### Notes

Answer the following in your own words:

**1. What is the responsibility of the `common` role?**

The common role handles the initial preparation of the Ubuntu server. It installs and configures the general system dependencies required by the deployment, ensuring that the environment is ready for the Nginx and EpicBook roles.
---

**2. Why should Nginx installation not be placed inside the `common` role?**

Nginx has a dedicated responsibility in this deployment and should therefore be managed by its own role. Keeping it separate follows the principle of separation of concerns, makes the automation more modular, and allows the Nginx configuration to be maintained independently of general server preparation.
---

**3. Why is `mysql-client` useful in this deployment?**

The MySQL client provides command-line tools for connecting to and interacting with the managed MySQL database. It is useful for testing database connectivity, authenticating against the database, and executing database operations from the application server.
---

# Task 7 — Create the `nginx` Role

## Goal

Create the `nginx` role to install Nginx and configure it as a reverse proxy for the EpicBook application.

Nginx will receive browser traffic on port `80` and forward it to the EpicBook Node.js application running on the VM.

### Evidence

#### Screenshot 13 — `roles/nginx/tasks/main.yml` showing Nginx installation and site configuration tasks

[`roles/nginx/tasks/main.yml` showing Nginx installation and site configuration tasks](screenshots/Ass5-13.png)
---

#### Screenshot 14 — `roles/nginx/templates/epicbook.conf.j2` showing the reverse proxy configuration

[`roles/nginx/templates/epicbook.conf.j2` showing the reverse proxy configuration](screenshots/Ass5-14.png)
---

### Notes

Answer the following in your own words:

**1. What is the responsibility of the `nginx` role?**

The nginx role automates the installation and configuration of Nginx on the Ubuntu server. It deploys the EpicBook site configuration, configures the reverse proxy, validates the Nginx configuration, and ensures that the service is running.
---

**2. Why is Nginx configured as a reverse proxy in this deployment?**

Nginx provides a public entry point for incoming HTTP traffic and forwards requests to the EpicBook Node.js application running internally on port 8080. This separates the public-facing web server from the application process and avoids the need to expose the Node.js port directly to the internet.
---

**3. Why should the application port come from `group_vars/web.yml` instead of being hard-coded?**

Defining the application port in group_vars/web.yml centralizes configuration and allows it to be reused across Ansible roles and templates. If the application port changes, it can be updated in one location rather than modifying several files. This improves consistency and maintainability.
---

# Task 8 — Create the `epicbook` Role

## Goal

Create the `epicbook` role to deploy the EpicBook application, connect it to the managed MySQL database, and run the application on port `8080` using PM2.

### Evidence

#### Screenshot 15 — `roles/epicbook/tasks/main.yml` showing application deployment tasks

[`roles/epicbook/tasks/main.yml` showing application deployment tasks](screenshots/Ass5-15.png)
---

#### Screenshot 16 — Task or file showing how the database connection is configured, with secrets hidden

[Task or file showing how the database connection is configured, with secrets hidden](screenshots/Ass5-16.png)
---

#### Screenshot 17 — Task or output showing the EpicBook application managed by PM2

[Task or output showing the EpicBook application managed by PM2](screenshots/Ass5-17.png)
---

### Notes

Answer the following in your own words:

**1. What is the responsibility of the `epicbook` role?**

The epicbook role automates the application deployment process. It handles retrieving the application source code, installing Node.js dependencies, configuring the database connection, preparing the database schema and data, and starting the application using PM2.
---

**2. Why is PM2 used for the EpicBook Node.js application?**

PM2 is used as a process manager to run the Node.js application in the background. It monitors the application process, supports automatic restarts after crashes, and provides process and log management capabilities. This improves application process reliability.
---

**3. Why should database passwords not be hard-coded in public files?**

Hard-coding database passwords in public files creates a security risk because anyone with access to the repository or configuration could obtain the credentials. This could lead to unauthorized database access and data compromise. Sensitive credentials should instead be encrypted or retrieved securely through a secrets-management solution. In this project, Ansible Vault was used to protect the database password.
---

**4. What does it mean for the application to run on port `8080` while Nginx listens on port `80`?**

It means the Node.js application listens internally on port 8080 while Nginx accepts incoming HTTP requests on port 80. Nginx forwards those requests to the application through its reverse-proxy configuration. Users access the application through the standard HTTP port without connecting directly to the Node.js service.
---

# Task 9 — Create Group Variables

## Goal

Create reusable variables for the EpicBook deployment.

The `group_vars/web.yml` file stores values that can be reused across the Ansible roles.

### Evidence

#### Screenshot 18 — `group_vars/web.yml` showing the application, PM2, and database variables, with passwords hidden or masked

[`group_vars/web.yml` showing the application](screenshots/Ass5-18.png)
---

### Notes

Answer the following in your own words:

**1. What is the purpose of `group_vars/web.yml`?**

group_vars/web.yml stores shared configuration variables for hosts belonging to the web inventory group. It centralizes application, process-management, and database configuration values so that multiple Ansible roles can reference consistent settings.
---

**2. Which values did you store in `group_vars/web.yml`?**

I centralized the application, Nginx, and managed MySQL configuration variables in group_vars/web.yml, including:
- Application: GitHub repository URL, deployment destination (/home/ubuntu/epicbook), application user (ubuntu), and application port (8080).
- PM2: Application process name (epicbook).
- Nginx: Server name (100.54.23.195).
- Managed MySQL: Database hostname, database name (epicbook), and database username (epicbook_admin).
- Database password reference: The password is retrieved through the encrypted Ansible Vault variable vault_db_password, rather than being stored in plain text.
This centralized configuration allows the Ansible roles to reuse consistent values and makes future changes easier to manage without modifying multiple task files.
---

**3. How did you handle the database password securely?**

I used Ansible Vault to encrypt the database password rather than storing it as plain text in the regular Ansible configuration. During troubleshooting, I identified a mismatch between the Vault password value and the Terraform-generated database password. I corrected the encrypted Vault value using the password generated by Terraform and verified that the values matched before proceeding with database authentication.
---

# Task 10 — Run the Ansible Playbook

## Goal

Run the Ansible playbook to configure the VM and deploy the EpicBook application.

The playbook should run the roles in this order:

1. `common`
2. `nginx`
3. `epicbook`

### Evidence

#### Screenshot 19 — Ansible playbook output showing the roles running

[Ansible playbook output showing the roles running](screenshots/Ass5-19.png)
---

#### Screenshot 20 — Final Ansible recap showing `failed=0`

[Final Ansible recap showing `failed=0`](screenshots/Ass5-20.png)
---

#### Screenshot 21 — Output of `ansible web -i inventory.ini -m command -a "systemctl is-active nginx" --become`

[Output of `ansible web -i inventory.ini -m command -a "systemctl is-active nginx" --become`](screenshots/Ass5-21.png)
---

#### Screenshot 22 — Output of `ansible web -i inventory.ini -m command -a "pm2 status"`

[Output of `ansible web -i inventory.ini -m command -a "pm2 status"](screenshots/Ass5-23.png)
---

#### Screenshot 23 — Output of `ansible web -i inventory.ini -m command -a "curl -I http://localhost:8080"`

[Output of `ansible web -i inventory.ini -m command -a "curl -I http://localhost:8080"`](screenshots/Ass5-23.png)
---

### Notes

Answer the following in your own words:

**1. What command did you run to execute the Ansible playbook?**

ansible-playbook -i inventory.ini site.yml --ask-vault-pass
---

**2. How do you know all roles completed successfully?**

The playbook executed the common, nginx, and epicbook roles in the specified order. The final recap showed ok=20, changed=4, unreachable=0, and failed=0, confirming successful execution.
---

**3. What proves that Nginx is active?**

The command systemctl is-active nginx returned active, confirming that the Nginx service is running on the server.
---

**4. What proves that PM2 is managing the EpicBook application?**

The pm2 status output showed the epicbook process with an online status, confirming that PM2 is managing the running Node.js application.
---

**5. What proves that the EpicBook application responds on port `8080`?**

The command curl -I http://localhost:8080 returned HTTP/1.1 200 OK, confirming that the application responds successfully on its configured port.
---

# Task 11 — Verify the EpicBook Deployment

## Goal

Verify that the EpicBook application is running, accessible in the browser, and connected to the managed MySQL database.

### Evidence

#### Screenshot 24 — Output of `curl -I http://<public_ip>`

[Output of `curl -I http://<public_ip>`](screenshots/Ass5-24.png)
---

#### Screenshot 25 — Output of the cart API test command

[Output of the cart API test command](screenshots/Ass5-25.png)
---

#### Screenshot 26 — Output of the `/cart` HTTP status check

[Output of the `/cart` HTTP status check](screenshots/Ass5-26.png)
---

#### Screenshot 27 — Browser showing the EpicBook application loaded from `http://<public_ip>`

[`http://<public_ip>`](screenshots/Ass5-27.png)
---

### Notes

Answer the following in your own words:

**1. What HTTP response did you receive from the public application URL?**

The public application URL returned HTTP 200 OK, confirming that the EpicBook application was successfully accessible through its public IP address.
---

**2. What did the cart API test prove?**

The cart endpoint test confirmed that Nginx successfully forwarded the request to the Node.js Express application, which returned the EpicBook cart page with a successful response.
---

**3. What did the `/cart` status check return?**

The /cart endpoint returned HTTP 200, confirming that the cart route was accessible and responding successfully through the public application URL.
---

**4. What issue did you face during verification, and how did you fix it?**

During verification, the browser initially displayed the default Nginx welcome page instead of the EpicBook application. I investigated the Nginx site configuration, enabled server blocks and reverse-proxy settings. After troubleshooting and further browser verification, the EpicBook application loaded correctly through the public IP address.
---

# LinkedIn Post Required

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://lnkd.in/p/gJu8tVpS
---

#### Screenshot — Published LinkedIn post

[Published LinkedIn post](screenshots/Ass5-28.png)
---

# Assignment Questions

Answer the following in your own words:

**1. Why is Terraform used for infrastructure provisioning?**

Terraform is used to define, provision, and manage cloud infrastructure through code. In this project, it automated the creation of AWS resources, including the EC2 instance, networking components, security groups, and managed MySQL database. This makes infrastructure deployment repeatable, consistent, and easier to maintain.
---

**2. Why are Ansible roles useful for production-style deployments?**

Ansible roles organize configuration tasks, templates, handlers, and variables into reusable components. In this project, roles such as common, nginx, and epicbook separated system configuration, web server setup, and application deployment. This modular structure improves maintainability, consistency, and scalability.
---

**3. What is the purpose of `group_vars/web.yml`?**

group_vars/web.yml defines variables that apply to hosts belonging to the web inventory group. It centralizes configuration values used by Ansible roles and playbooks, making it easier to manage application settings without duplicating values across multiple files.
---

**4. Why should database passwords not be committed to GitHub?**

Committing database passwords to GitHub exposes sensitive credentials to unauthorized users. Attackers could use them to access the database, compromise application data, or cause financial damage. Sensitive values should instead be stored securely using Ansible Vault or a cloud secrets-management service.
---

**5. What is the purpose of Nginx in this deployment?**

Nginx acts as a reverse proxy, receiving public HTTP requests on port 80 and forwarding them to the EpicBook Node.js application running on port 8080. This provides a controlled entry point for application traffic without requiring the Node.js application port to be publicly exposed.
---

**6. Why should the managed MySQL database not be publicly accessible?**

The managed MySQL database should remain private to reduce its exposure to internet-based attacks. Only authorized application resources should be allowed to connect to it through the appropriate security group rules on port 3306. This limits unauthorized access and protects sensitive application data.
---

**7. Why is PM2 used for the EpicBook Node.js application?**

PM2 is a process manager that keeps the Node.js application running in the background. It can automatically restart the application if it crashes, manage application processes, and provide access to application logs. It improves application process management and operational reliability.
---

**8. What does idempotency mean in Ansible?**

Idempotency means that running an Ansible playbook multiple times produces the same desired system state without making unnecessary changes. Ansible checks the current state of managed resources and only applies changes when required. This makes deployments more predictable and reliable.
---

**9. What issue did you face during the deployment, and how did you fix it?**

During deployment verification, the browser initially displayed the default Nginx welcome page instead of the EpicBook application. I investigated the active Nginx server configuration and reverse-proxy settings, then verified the Nginx and Node.js responses using local and public HTTP requests. Further browser verification confirmed that the EpicBook application was loading correctly through the public IP address.
---

**10. What security improvement would you make before using this setup in production?**

I would implement HTTPS using a valid TLS certificate so that communication between users and the application is encrypted. I would also strengthen secrets management by storing database credentials in AWS Secrets Manager or Ansible Vault, rather than exposing them in configuration files or source control.
---

# Required Files

Confirm that the following files are included in your GitHub repository or assignment folder:

- [ ] `README.md`
- [ ] Terraform files under either `terraform/azure/` or `terraform/aws/`
- [ ] `ansible/ansible.cfg`
- [ ] `ansible/inventory.ini`
- [ ] `ansible/site.yml`
- [ ] `ansible/group_vars/web.yml`
- [ ] `ansible/roles/common/tasks/main.yml`
- [ ] `ansible/roles/nginx/tasks/main.yml`
- [ ] `ansible/roles/nginx/templates/epicbook.conf.j2`
- [ ] `ansible/roles/epicbook/tasks/main.yml`

---

# Submission Instructions

- Add all required screenshots in your submission.
- Full Name must be visible in required screenshots.
- Mention the cloud provider used: Azure or AWS.
- Add the VM public IP address.
- Add the final application URL.
- Add Terraform output proof.
- Add Ansible role tree proof.
- Add all required notes and assignment question answers.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, passwords, cloud credentials, database credentials, Terraform state files, subscription IDs, or account IDs.

---

# Completion Checklist

- [ ] Task 1: Project folder layout created
- [ ] Task 2: Terraform infrastructure provisioned
- [ ] Task 3: SSH key-based access verified
- [ ] Task 4: Ansible inventory and configuration created
- [ ] Task 5: Main Ansible playbook created
- [ ] Task 6: `common` role created
- [ ] Task 7: `nginx` role created
- [ ] Task 8: `epicbook` role created
- [ ] Task 9: Group variables created
- [ ] Task 10: Ansible playbook run completed
- [ ] Task 11: EpicBook deployment verified
- [ ] Terraform files created under only one cloud provider folder
- [ ] One Ubuntu VM was created
- [ ] One managed MySQL database was created
- [ ] SSH port `22` is restricted to the controller public IP
- [ ] HTTP port `80` is accessible
- [ ] MySQL port `3306` is not publicly open
- [ ] `ansible web -i inventory.ini -m ping` returns `SUCCESS`
- [ ] `site.yml` calls the roles in the correct order
- [ ] Database secrets are hidden or handled securely
- [ ] Nginx is active
- [ ] PM2 shows the EpicBook application running
- [ ] EpicBook responds on port `8080`
- [ ] Public URL loads in the browser
- [ ] Cart API verification works
- [ ] Playbook completes with `failed=0`
- [ ] Screenshots 1–27 are included
- [ ] Assignment questions are answered
- [ ] LinkedIn post published
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