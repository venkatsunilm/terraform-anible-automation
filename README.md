---

# **Cloud Environment Provisioning and Configuration**

This repository contains two sub-projects for automating the provisioning and configuration of a cloud environment:

1. **Terraform Project**: Automates the provisioning of cloud infrastructure using CSC's OpenStack `cPouta` service.
2. **Ansible Project**: Configures and deploys a load balancer and web servers across the provisioned infrastructure.

---

## **Table of Contents**
- [Overview](#overview)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
  - [1. Terraform Setup](#1-terraform-setup)
  - [2. Ansible Configuration](#2-ansible-configuration)
- [Expected Results](#expected-results)
- [Troubleshooting](#troubleshooting)

---

## **Overview**

This project demonstrates the complete lifecycle of deploying and configuring a cloud environment:

1. **Provision Infrastructure**: Use Terraform to create a cloud environment with:
   - A jumphost with public IP (VM1) serving as a load balancer.
   - Multiple private web servers (VM2, VM3, VM4) connected via a private network.

2. **Configure Environment**: Use Ansible to:
   - Install and configure Nginx on the jumphost as a load balancer.
   - Install and configure Nginx on web servers to serve static content.
   - Set up secure and consistent configurations across all VMs.

---

## **Project Structure**

```
.
├── README.md             # Main project README
├── terraform/            # Terraform project for infrastructure provisioning
│   ├── README.md         # Terraform-specific README
│   ├── main.tf           # Terraform configuration file
│   ├── variables.tf      # Input variables for Terraform
│   └── providers.tf      # OpenStack provider configuration
└── ansible/              # Ansible project for configuration management
    ├── README.md         # Ansible-specific README
    ├── inventory.ini     # Ansible inventory file
    ├── webservers.yml    # Playbook for load balancer and web servers
    ├── files/            # Configuration files
    │   ├── nginx_proxy.conf
    │   ├── nginx.conf

```

---

## **Getting Started**

### **1. Terraform Setup**

#### **Step 1: Install Terraform**
Follow the instructions in `terraform/README.md` to install and configure Terraform.

#### **Step 2: Provision the Infrastructure**
1. Navigate to the Terraform project directory:
   ```bash
   cd terraform/
   ```
2. Initialize Terraform:
   ```bash
   terraform init
   ```
3. Plan and apply the configuration:
   ```bash
   terraform apply
   ```

### **2. Ansible Configuration**

#### **Step 1: Install Ansible**
Follow the instructions in `ansible/README.md` to install Ansible.

#### **Step 2: Configure Load Balancer and Web Servers**
1. Navigate to the Ansible project directory:
   ```bash
   cd ansible/
   ```
2. Test connectivity to all VMs:
   ```bash
   ansible -i inventory.ini -m ping all
   ```
3. Run the Ansible playbook:
   ```bash
   ansible-playbook -i inventory.ini webservers.yml
   ```

---

## **Expected Results**

- **Jumphost (VM1)**:
  - Acts as a load balancer using Nginx.
  - Forwards HTTP traffic to the web servers in a round-robin manner.

- **Web Servers (VM2, VM3, VM4)**:
  - Serve the same static HTML content.
  - Are accessible only from the private network.

You can test the setup by accessing the public IP of the jumphost.

---

## **Troubleshooting**

1. **Terraform Issues**:
   - Ensure your OpenStack credentials are correctly set in `providers.tf`.
   - Check for typos in the `variables.tf` file.

2. **Ansible Issues**:
   - Verify SSH access to all VMs.
   - Check that `nginx` is running on all servers.

3. **Network Issues**:
   - Ensure that security groups allow the necessary traffic:
     - Port `22` for SSH.
     - Port `80` for HTTP.

Refer to the individual README files for more detailed troubleshooting steps.

---

This project provides a modular and automated approach to cloud environment setup and configuration, making it scalable and reusable for various use cases.