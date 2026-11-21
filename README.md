# Azure Linux VM Deployment and Nginx Web Server Configuration

## Project Overview

This project demonstrates the deployment and configuration of an Ubuntu Linux Virtual Machine on Microsoft Azure, followed by the installation and configuration of Nginx to host a custom static webpage.

The objective was to gain practical experience with cloud infrastructure provisioning, Linux server administration, remote server access, network security, and web server management.

The project involved creating a Linux VM, establishing a secure SSH connection, configuring Network Security Group (NSG) rules, managing the Ubuntu environment, installing Nginx, and deploying a custom HTML webpage accessible through the VM's public IP address.

After successfully completing and documenting the deployment, all Azure resources associated with the project were deleted to prevent unnecessary ongoing costs.

---

## Technologies and Services Used

| Technology              | Purpose                             |
| ----------------------- | ----------------------------------- |
| Microsoft Azure         | Cloud infrastructure provider       |
| Azure Virtual Machines  | Hosting the Linux server            |
| Ubuntu 24.04 LTS        | Server operating system             |
| SSH                     | Secure remote server access         |
| SSH Key Pair            | Key-based authentication            |
| Network Security Groups | Controlling inbound network traffic |
| Nginx                   | Web server                          |
| HTML                    | Custom webpage                      |
| Windows PowerShell      | SSH client and terminal             |

---

## Architecture

The project followed this basic architecture:

```text
                 Internet
                    |
                    |
             Public IP Address
                    |
                    |
          Azure Network Security
             Group (NSG)
                    |
             ----------------
             |              |
          SSH : 22       HTTP : 80
             |              |
             |              |
          Ubuntu Linux Virtual Machine
                    |
                  Nginx
                    |
             /var/www/html/
                    |
                index.html
                    |
             Custom Webpage
```

The NSG controlled access to the virtual machine. SSH traffic was restricted to my public IP address, while HTTP traffic was allowed so that the hosted webpage could be accessed through a browser.

---

## Implementation Steps

### 1. Provisioning the Azure Virtual Machine

Created a Linux virtual machine using Microsoft Azure.

Configuration:

* VM Name: `linux-server-01`
* Operating System: Ubuntu 24.04 LTS
* Region: Central India
* VM Size: Standard B2pts_v2
* Architecture: ARM64
* vCPUs: 2
* RAM: 1 GiB
* OS Disk: 30 GiB Standard SSD
* Authentication: SSH Public Key

The VM was deployed inside an Azure Virtual Network with its own subnet and Network Security Group.

### 2. Configuring SSH Access

Generated an Ed25519 SSH key pair and used the private key to establish a secure connection from Windows PowerShell.

Connected to the virtual machine using:

```bash
ssh -i "./linux-server-key.pem" azureuser@<PUBLIC_IP>
```

Initially, SSH authentication failed because the private key file had overly permissive Windows access permissions.

Resolved the issue by modifying the file's access control permissions so that only the current Windows user could read the private key.

Successfully established an SSH connection to the Ubuntu server.

### 3. Configuring Network Security Groups

Configured inbound security rules to control network access to the virtual machine.

| Rule | Port | Protocol | Source       | Action |
| ---- | ---: | -------- | ------------ | ------ |
| SSH  |   22 | TCP      | My public IP | Allow  |
| HTTP |   80 | TCP      | Any          | Allow  |

**SSH (Port 22):** Restricted access to my public IP address to reduce unnecessary exposure of the remote administration service.

**HTTP (Port 80):** Allowed inbound web traffic so that visitors could access the hosted webpage.

Default outbound security rules were retained.

### 4. Updating and Inspecting Ubuntu

Updated the package repositories and installed available package updates.

```bash
sudo apt update
sudo apt upgrade -y
```

Inspected the operating system and server resources using:

```bash
lsb_release -a
uname -a
free -h
df -h
ip addr
```

These commands provided information about:

* Ubuntu version and distribution.
* Linux kernel and system architecture.
* Available and used memory.
* Disk capacity and usage.
* Network interfaces and IP addresses.

### 5. Installing and Configuring Nginx

Installed Nginx using Ubuntu's package manager.

```bash
sudo apt install nginx -y
```

Checked the service status:

```bash
sudo systemctl status nginx
```

Verified that the service was active and running.

Enabled Nginx to start automatically when the virtual machine boots:

```bash
sudo systemctl enable nginx
```

Tested the web server locally:

```bash
curl http://localhost
```

The command returned the default Nginx HTML response, confirming that the web server was responding correctly.

### 6. Testing Public Web Access

Opened the VM's public IP address in a web browser using HTTP:

```text
http://<PUBLIC_IP>
```

Successfully accessed the default Nginx welcome page from outside the virtual machine.

This verified that:

* Nginx was running.
* The HTTP inbound rule was configured correctly.
* The VM was reachable through its public IP address.
* The web server was serving content over HTTP.

### 7. Deploying a Custom HTML Webpage

Located the default Nginx web root:

```bash
ls -la /var/www/html
```

Edited the existing `index.html` file:

```bash
sudo nano /var/www/html/index.html
```

Replaced the default Nginx webpage with a custom HTML page containing:

* Welcome to My Azure Linux Server!
* Ubuntu 24.04 on Microsoft Azure.
* Server Status: Online.

The custom webpage was successfully displayed through the VM's public IP address.

A recreated copy of the HTML file is available in the `website` directory.

---

## Challenges Faced

### 1. SSH Private Key Permissions

The initial SSH connection failed because Windows reported that the private key permissions were too open.

**Resolution:** Modified Windows file permissions to restrict access to the private key.

### 2. Network Security Configuration

Understanding the relationship between Azure Network Security Groups and inbound traffic was an important part of the deployment.

**Resolution:** Configured separate inbound rules for SSH and HTTP, restricting SSH access while allowing public HTTP access.

### 3. Web Server Accessibility

The Nginx service was initially tested locally before attempting public access.

**Resolution:** Verified the service using `systemctl` and `curl`, then confirmed external accessibility through the VM's public IP address.

---

## Repository Structure
azure-linux-vm-nginx/
│
├── README.md
│
├── screenshots/
│   ├── 01-azure-vm-overview.jpeg
│   ├── 02-vm-networking-and-security.jpeg
│   ├── 03-ssh-connection-command.jpeg
│   ├── 07-linux-system-information.jpeg
│   ├── 08-nginx-installation.jpeg
│   ├── 09-nginx-service-status.jpeg
│   ├── 10-nginx-service-and-localhost-testing.jpeg
│   ├── 11-nginx-public-access.jpeg
│   ├── 12-nsg-inbound-rules.jpeg
│   ├── 13-custom-html-deployment.jpeg
│   └── 14-custom-webpage-file-and-editing.jpeg
│
└── website/
    └── index.html

---

## Resource Cleanup

After completing the deployment and capturing the project documentation, I deleted the virtual machine and all associated Azure resources.

This included the resources created for the project, such as the VM, managed disk, public IP, network interface, Network Security Group, and Virtual Network.

Resource cleanup was performed to avoid unnecessary ongoing Azure charges.

---

## Key Learnings

Through this project, I gained practical experience with:

* Provisioning Linux virtual machines on Microsoft Azure.
* Understanding Azure networking fundamentals.
* Establishing secure SSH connections using key-based authentication.
* Managing Linux packages and system services.
* Inspecting Linux system resources.
* Configuring Network Security Groups and inbound traffic.
* Installing and managing Nginx.
* Hosting static websites on Linux servers.
* Troubleshooting SSH and networking issues.
* Managing cloud resources and performing infrastructure cleanup.

---

## Project Status

**Completed**

All deployment, configuration, testing, and documentation activities were completed successfully.
