# Assignment 03 — Deploy a Static Website to Multiple Servers Using a Multi-Play Ansible Playbook

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Student Details

**Full Name:** Add your full name here  
**Cloud Platform Used:** AWS / Azure  
**Server 1 URL:** `http://<SERVER_1_PUBLIC_IP>`  
**Server 2 URL:** `http://<SERVER_2_PUBLIC_IP>`

---

## Purpose

In this assignment, you will create a multi-play Ansible playbook to install Nginx, deploy a static website to two Ubuntu servers, and verify that the website is accessible from both servers.

You may use either AWS EC2 instances or Azure Virtual Machines as your managed servers.

---

# Task 1 — Create the Project Structure

## Goal

Create the required folders and files for the Ansible project.

## Evidence

### Screenshot 1 — Terminal or VS Code showing the complete `static-web` project structure

[Terminal or VS Code showing the complete `static-web` project structure](screenshots/Ass3-01.png)

---

# Task 2 — Configure the Ansible Inventory

## Goal

Add both Ubuntu servers to the Ansible inventory.

## Evidence

### Screenshot 2 — Output of `ansible-inventory -i inventory.ini --graph` showing `web1` and `web2`

[showing `web1` and `web2`](screenshots/Ass3-02.png)
---

## Configuration File

Copy and paste the complete contents of your `inventory.ini` file below:

```ini
[web]
web1 ansible_host=20.109.158.29
web2 ansible_host=20.59.58.254

[web:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/terraform-azure-vm-key

```

---

# Task 3 — Verify Ansible Connectivity

## Goal

Confirm that the Ansible controller can connect to both servers.

## Evidence

### Screenshot 3 — Ansible ping output showing `SUCCESS` and `pong` for both servers

[Ansible ping output showing `SUCCESS` and `pong` for both servers](screenshots/Ass3-03.png)
---

# Task 4 — Download and Personalize the Static Website

## Goal

Download `index.html` to the Ansible controller and personalize the website with your full name.

## Evidence

### Screenshot 4 — Edited `files/index.html` showing the footer line with your full name

[Edited `files/index.html` showing the footer line with your full name](screenshots/Ass3-04.png)
---

# Task 5 — Create the Multi-Play Ansible Playbook

## Goal

Create a single Ansible playbook containing separate plays for installation, deployment, and verification.

## Configuration File

Copy and paste the complete contents of your `site.yml` file below:

```yaml
---
- name: Install and start Nginx on web servers
  hosts: web
  become: true
  tasks:
    - name: Update apt cache and install Nginx
      ansible.builtin.apt:
        name: nginx
        state: present
        update_cache: true
        cache_valid_time: 3600

    - name: Ensure Nginx is enabled and running
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true

- name: Deploy the static website
  hosts: web
  become: true
  tasks:
    - name: Copy website to Nginx document root
      ansible.builtin.copy:
        src: files/index.html
        dest: /var/www/html/index.html
        owner: www-data
        group: www-data
        mode: "0644"
      notify: Reload nginx

  handlers:
    - name: Reload nginx
      ansible.builtin.service:
        name: nginx
        state: reloaded

- name: Verify websites return HTTP 200
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Check each web server
      ansible.builtin.uri:
        url: "http://{{ hostvars[item].ansible_host }}"
        status_code: 200
        return_content: false
      loop: "{{ groups['web'] }}"
      register: website_checks

    - name: Confirm all website checks succeeded
      ansible.builtin.assert:
        that:
          - item.status == 200
        success_msg: "Website is responding with HTTP 200."
      loop: "{{ website_checks.results }}"

```

---

# Task 6 — Validate the Playbook Syntax

## Goal

Check the playbook for YAML or Ansible syntax errors before running it.

## Evidence

### Screenshot 5 — Successful syntax-check output showing `playbook: site.yml`

[Successful syntax-check output showing `playbook: site.yml`](screenshots/Ass3-05.png)
---

# Task 7 — Run the Multi-Play Playbook

## Goal

Install Nginx, deploy the website, and verify both servers in one playbook run.

## Evidence

### Screenshot 6 — Play 3 verification showing HTTP `200` for both servers

[Play 3 verification showing HTTP `200` for both servers](screenshots/Ass3-06.png)
---

### Screenshot 7 — Final play recap showing `unreachable=0` and `failed=0` for `web1`, `web2`, and `localhost`

[Final play recap showing `unreachable=0` and `failed=0` for `web1`, `web2`](screenshots/Ass3-07.png)
---

# Task 8 — Verify Idempotency

## Goal

Run the playbook again and confirm that it does not make unnecessary changes.

## Evidence

### Screenshot 8 — Second playbook run showing the play recap with `changed=0`, `unreachable=0`, and `failed=0` for both web servers

[Second playbook run showing the play recap](screenshots/Ass3-08.png)
---

# Task 9 — Test Both Websites Manually

## Goal

Confirm that the static website is accessible from both public IP addresses.

## Evidence

### Screenshot 9 — `curl -I` output showing HTTP `200 OK` from both servers

[`curl -I` output showing HTTP `200 OK` from both servers](screenshots/Ass3-09.png)
---

### Screenshot 10 — Browser showing the website from Server 1 with the public IP and your full name visible

[Browser showing the website from Server 1](screenshots/Ass3-10.png)
---

### Screenshot 11 — Browser showing the website from Server 2 with the public IP and your full name visible

[Browser showing the website from Server 2](screenshots/Ass3-11.png)
---

## Website URLs

Add both deployed website URLs below:

```text
Server 1: http://20.109.158.29/
Server 2: http://20.59.58.254/
```

---

# Task 10 — Complete the Project README

## Goal

Document how the project works and record what you learned.

## README Content

Copy and paste the complete contents of your `README.md` file below:

```markdown
# Assignment 03: Deploy a Static Website to Multiple Servers Using Ansible

## Project Overview

This project demonstrates how to use Ansible to deploy the same static website to multiple Azure virtual machines. I used a multi-play Ansible playbook to install and configure Nginx, deploy the website, and verify that both web servers respond successfully over HTTP.

The goal was to automate website deployment so that the same configuration and website content could be applied consistently across multiple servers.

## Technologies Used

- **Microsoft Azure:** Hosted the virtual machines.
- **Terraform:** Provisioned the cloud infrastructure.
- **Ansible:** Automated server configuration and website deployment.
- **Nginx:** Served the static website over HTTP.
- **Ubuntu Linux:** Operating system on the web servers.
- **YAML:** Used to define the Ansible playbook.
- **SSH:** Enabled secure communication between Ansible and the remote servers.

## Project Structure

```text
static-web/
├── files/
│   └── index.html
├── inventory.ini
├── site.yml
└── README.md
```

- `files/index.html`: Contains the static website content, including my name in the footer.
- `inventory.ini`: Defines the web server inventory, IP addresses, SSH username, and private-key path.
- `site.yml`: Contains the multi-play Ansible playbook used to install Nginx, deploy the website, and verify the deployment.
- `README.md`: Documents the project, execution steps, and lessons learned.

## Infrastructure and Inventory

I used two Azure virtual machines as web servers:

- `web1`
- `web2`

Both servers run Ubuntu and are managed remotely using SSH. The Ansible inventory groups them under `[web]`, allowing the same tasks to run against both servers.

The SSH private key is stored outside the project repository, and the private key file permissions are restricted. Sensitive credentials should never be committed to Git.

## How the Playbook Works

The `site.yml` file contains three plays.

### Play 1: Install and Start Nginx

The first play targets the `web` inventory group and uses privilege escalation with `become: true`.

It updates the APT package cache when necessary, installs Nginx if it is not already installed, and ensures the Nginx service is enabled and running.

### Play 2: Deploy the Website

The second play copies `files/index.html` from my Ansible control machine to `/var/www/html/index.html` on both web servers.

The file is assigned to the `www-data` user and group with permission mode `0644`.

A handler named `Reload nginx` reloads Nginx when the website file changes. This avoids reloading the service unnecessarily when no change has occurred.

### Play 3: Verify the Deployment

The third play runs on the Ansible control machine using `connection: local`.

It loops through the web servers in the inventory and sends HTTP requests to each server using Ansible's `uri` module. It checks that each server returns HTTP status code `200`.

An assertion confirms that the website checks succeeded.

## Steps to Run the Project

Run the following commands from the project directory in the WSL Ubuntu terminal.

### 1. Verify the Inventory

```bash
ansible-inventory -i inventory.ini --graph
```

This confirms that both web servers are listed in the inventory.

### 2. Test SSH Connectivity

```bash
ansible web -i inventory.ini -m ping
```

Both servers should return `SUCCESS` and `pong`.

### 3. Validate the Playbook Syntax

```bash
ansible-playbook -i inventory.ini site.yml --syntax-check
```

This checks the playbook syntax before execution.

### 4. Deploy the Website

```bash
ansible-playbook -i inventory.ini site.yml
```

Ansible installs and starts Nginx, copies the website to both servers, and verifies the HTTP responses.

### 5. Test Idempotency

Run the playbook again:

```bash
ansible-playbook -i inventory.ini site.yml
```

On the successful second run, both web servers reported `changed=0`, `unreachable=0`, and `failed=0`. This demonstrated that Ansible did not need to make additional changes when the servers were already in the desired state.

### 6. Verify the Website Manually

```bash
curl -I http://WEB1_PUBLIC_IP
curl -I http://WEB2_PUBLIC_IP
```

Replace the placeholders with the actual public IP addresses of the web servers. Both requests should return an HTTP `200 OK` response.

The website can also be opened in a browser using each server's public IP address.

## Results

The deployment completed successfully.

- Both Azure web servers were reachable through Ansible.
- Nginx was installed and running on both servers.
- The same website was deployed to both servers.
- The automated HTTP checks returned status code `200`.
- The second playbook run completed without changes on either web server.
- Both servers served the website with my name displayed in the footer.

## What I Learned

1. **Ansible inventory management:** I learned how to group multiple servers in an inventory and apply the same tasks to them.
2. **SSH connectivity:** I learned the importance of using the correct private key, setting appropriate file permissions, and ensuring SSH access is allowed.
3. **Multi-play playbooks:** I learned how to separate server setup, application deployment, and deployment verification into distinct plays.
4. **Privilege escalation:** I learned why `become: true` is necessary for administrative operations such as installing packages and deploying files into protected directories.
5. **Handlers:** I learned how handlers respond to changes and help avoid unnecessary service reloads.
6. **Idempotency:** I confirmed that rerunning a playbook should not make unnecessary changes when the target servers already match the desired state.
7. **Automated verification:** I learned how to use the `uri` module and assertions to check that the website is responding correctly.
8. **Infrastructure and configuration management:** I gained practical experience using Terraform to provision Azure infrastructure and Ansible to configure the provisioned servers.

## Conclusion

This assignment helped me understand how infrastructure provisioning and configuration management work together. Terraform provides the cloud infrastructure, while Ansible configures the servers and deploys the website consistently.

Using a multi-play playbook made the deployment process repeatable, easier to verify, and more efficient than manually configuring each server individually.
```

---

# LinkedIn Post Required

## Evidence

### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://lnkd.in/p/eNDdYwcA
---

### Screenshot — Published LinkedIn post

[Published LinkedIn post](screenshots/Ass3-12.png)
---

# Assignment Questions

Answer the following in your own words:

**1. What issue did you face while completing this assignment, and how did you fix it?**

# Assignment Questions

**1. What issue did you face while completing this assignment, and how did you fix it?**

One issue I faced was SSH connectivity between my Ansible control machine and the Azure virtual machines. I discovered that I was using the wrong SSH private key. I fixed the problem by identifying the correct private key, copying it into my WSL SSH directory, setting the appropriate file permissions, and updating my Ansible inventory. After that, both web servers responded successfully to the Ansible ping test.

---

**2. What did you learn from this assignment?**

I learned how to use Ansible to manage multiple Azure virtual machines, install and configure Nginx, and deploy the same static website to different servers. I also learned how to structure a multi-play playbook, use handlers, verify HTTP responses, and test idempotency by running the playbook more than once.

---

**3. Why is it useful to split installation, deployment, and verification into separate plays?**

Splitting these tasks into separate plays makes the playbook easier to understand, maintain, and troubleshoot. Each play has a specific responsibility: installing and starting Nginx, deploying the website, or verifying that the website is accessible. This separation also makes it easier to identify which stage has a problem if something fails.

---

**4. What is one benefit of using the Ansible `copy` module instead of cloning the website directly from Git on every managed server?**

One benefit is that the `copy` module allows me to manage the website files from a central control machine and deploy the same version to all managed servers. The servers do not need to clone the Git repository themselves, and the deployment does not depend on each server accessing the Git repository directly.

---

**5. What does idempotency mean in this assignment?**

Idempotency means that running the same Ansible playbook repeatedly produces the desired state without making unnecessary changes. In this assignment, the second run reported `changed=0` on both web servers, showing that the website and Nginx configuration were already in the required state.

---

**6. What does the Ansible `uri` module verify in Play 3?**

The `uri` module sends HTTP requests to both web servers and checks that each server returns HTTP status code `200`. This confirms that the website is responding successfully over HTTP after deployment.

---

# Required Files

Confirm that the following files are included in your assignment folder:

- [ ] `inventory.ini`
- [ ] `site.yml`
- [ ] `files/index.html`
- [ ] `README.md`

---

# Submission Instructions

- Add all required screenshots in the correct order.
- Full Name must be visible in required screenshots.
- Include both deployed website URLs.
- Paste `inventory.ini`, `site.yml`, and `README.md` as editable text.
- Answer all assignment questions clearly in your own words.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, passwords, cloud account IDs, or other sensitive information.

---

# Completion Checklist

- [ ] Task 1: `static-web` folder structure is complete
- [ ] Task 2: Both servers are listed under the `[web]` group in `inventory.ini`
- [ ] Task 2: Inventory graph shows `web1` and `web2`
- [ ] Task 3: Ansible ping returns `SUCCESS` and `pong` for both servers
- [ ] Task 4: `files/index.html` contains your full name
- [ ] Task 5: `site.yml` contains three separate plays
- [ ] Task 5: Play 1 installs, starts, and enables Nginx
- [ ] Task 5: Play 2 deploys `index.html` using the `copy` module
- [ ] Task 5: Nginx reload handler is included
- [ ] Task 5: Play 3 verifies both web servers from the controller
- [ ] Task 6: Playbook syntax check passes
- [ ] Task 7: First playbook run completes with `unreachable=0` and `failed=0`
- [ ] Task 7: URI verification returns HTTP `200` for both servers
- [ ] Task 8: Second playbook run demonstrates idempotency
- [ ] Task 8: Second run shows `changed=0` for both web servers
- [ ] Task 9: Both `curl -I` commands return HTTP `200 OK`
- [ ] Task 9: Website loads from Server 1
- [ ] Task 9: Website loads from Server 2
- [ ] Task 9: Full name is visible on both deployed websites
- [ ] Task 10: `README.md` contains all required explanations
- [ ] Screenshots 1–11 are included
- [ ] `inventory.ini`, `site.yml`, and `README.md` are pasted as editable text
- [ ] Both website URLs are included
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