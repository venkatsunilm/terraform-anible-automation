### **README for Ansible Exercise**

---

# **Ansible Configuration for Load Balancer and Web Servers**

This repository contains Ansible playbooks and configurations for automating the deployment and configuration of a load balancer and web servers in a multi-VM setup. The setup is designed for a cPouta cloud environment and focuses on Nginx load balancing and web server configuration.

---

## **Objectives**

1. Automate the configuration of:
   - **Load Balancer (Nginx)** on the jumphost.
   - **Web Servers (Nginx)** on private VMs.
2. Distribute HTTP traffic from the load balancer to the web servers in a round-robin fashion.
3. Ensure secure and consistent configurations across all VMs.

---

## **Setup Overview**

- **Jumphost (VM1)**:
  - Acts as a load balancer using Nginx.
  - Public IP for external access.
  - Forwards traffic to web servers on the private network.

- **Web Servers (VM2, VM3, VM4)**:
  - Serve static content via Nginx.
  - Private IPs only (accessible from the jumphost).

---

## **Directory Structure**

```
ansible/
├── inventory.ini        # Ansible inventory file
├── webservers.yml       # Ansible playbook for load balancer and web servers
├── files/               # Configuration files
│   ├── nginx_proxy.conf # Nginx config for load balancer
│   ├── nginx.conf       # Nginx config for web servers

```

---

## **Prerequisites**

1. **Ansible Installed**:
   - Ensure Ansible is installed on your local machine or control node.
   - Install Ansible:
     ```bash


     sudo apt update
     sudo apt install ansible
     ```

2. **SSH Access**:
   - Ensure SSH access is configured for all VMs using the `id_rsa` private key.

3. **VM Connectivity**:
   - Jumphost must have public and private IPs.
   - Web servers must have private IPs accessible from the jumphost.

4. **Inventory File Setup**:
   - Update `inventory.ini` with the details of your VMs:
     ```ini
     [csc_proxy]
     jumphost ansible_host=<jumphost_public_ip> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa

     [csc_vms]
     vm2 ansible_host=<vm2_private_ip> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
     vm3 ansible_host=<vm3_private_ip> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
     vm4 ansible_host=<vm4_private_ip> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
     ```

---

## **How to Run**

1. **Check Connectivity**:
   - Test Ansible connectivity to all VMs:
     ```bash
     ansible -i inventory.ini -m ping all
     ```

2. **Run the Playbook**:
   - Execute the Ansible playbook:
     ```bash
     ansible-playbook -i inventory.ini webservers.yml
     ```

---

## **Playbook Details**

### **Play 1: Configure Load Balancer on Jumphost**
- **Install Nginx**: Ensures Nginx is installed on the jumphost.
- **Copy Configuration**: Deploys `nginx_proxy.conf` to `/etc/nginx/nginx.conf`.
- **Restart Nginx**: Applies the load balancer configuration.

### **Play 2: Configure Web Servers (VM2, VM3, VM4)**
- **Install Nginx**: Ensures Nginx is installed on all web servers.
- **Copy Configuration**: Deploys `nginx.conf` to `/etc/nginx/nginx.conf`.
- **Enable Configuration**: Links default site configuration.
- **Deploy Content**: Copies `index.html` to `/var/www/html/`.
- **Restart Nginx**: Applies the web server configuration.

---

## **Configuration Files**

1. **`nginx_proxy.conf`** (Load Balancer):
   - Configures upstream web servers for round-robin traffic distribution.

2. **`nginx.conf`** (Web Servers):
   - Basic Nginx configuration for serving static content.

3. **`index.html`**:
   - Sample static HTML page for testing.

---

## **Verification**

1. **Test Load Balancer**:
   - From your local machine:
     ```bash
     curl -I http://<jumphost_public_ip>/
     ```

2. **Test Web Servers**:
   - From the jumphost:
     ```bash
     curl -I http://<vm2_private_ip>/
     curl -I http://<vm3_private_ip>/
     curl -I http://<vm4_private_ip>/
     ```

---

## **Expected Results**

- Accessing the jumphost's public IP serves content from one of the web servers in a round-robin manner.
- All web servers should serve the same static HTML content.

---

## **Troubleshooting**

- **Nginx Not Running**:
  - Check the status:
    ```bash
    sudo systemctl status nginx
    ```
  - Restart if necessary:
    ```bash
    sudo systemctl restart nginx
    ```

- **Connection Issues**:
  - Verify security groups allow necessary traffic:
    - Port `80` and `443` for HTTP/HTTPS on the jumphost.
    - Port `80` from the jumphost to the web servers.

---