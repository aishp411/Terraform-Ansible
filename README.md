# AWS MERN Deployment with Terraform and Ansible

This project automates MERN stack deployment on AWS using Terraform for infrastructure creation and Ansible for server configuration and app deployment. It includes provisioning VPCs, EC2 instances, security groups, and setting up Node.js, MongoDB, and the application environment.

---
> Application repo used https://github.com/UnpredictablePrashant/TravelMemory

---
### 1) Terraform (VPC, Subnets, NAT, SGs, EC2)
Edit `terraform/variables.tf`:
- Add keypair name in the variable 
Make sure the keypair exist in the aws console, if not do create it.
```bash
cd terraform
terraform init
terraform plan 
terraform apply -auto-approve
```
Copy the outputs: `web_public_ip`, `db_private_ip`.

### What Terraform is doing
- Creates a VPC to host the entire infrastructure.
- Sets up two subnets — a public subnet for the web server and a private subnet for the database.
- Creates and associates route tables, including Internet access for the public subnet and NAT access for the private subnet.
- Provisions two EC2 instances — one in the public subnet (web) and one in the private subnet (database).
- Configures Internet Gateway and NAT Gateway for proper network flow.
- Sets up security groups for controlled access between web and database servers.
- Outputs useful information like the public IP of the web server and private IP of db server.
---
### Security hardening 
- SSH (22) access to the web server is restricted to your IP only (my_ip_cidr in variables.tf).
- The database EC2 has no public IP; MongoDB port 27017 is allowed only from the web server’s security group.
- MongoDB authentication is enabled, and the application uses a least-privilege user.
- UFW firewall is enabled on both hosts; only HTTP is allowed publicly on the web server and SSH for admin access.

---
### 2) Ansible (MongoDB, Node/Nginx, App)
Edit `ansible/inventory.ini`:
- Replace `WEB_PUBLIC_IP` with the Terraform output.
- Replace `DB_PRIVATE_IP` with the Terraform output.

Then run:
```bash
cd ../ansible
ansible-playbook site.yml
```

### What Ansible is doing
- Copies backend code to /opt/travelmemory/backend on the server
- Creates a PM2 ecosystem config with correct environment variables 
- Starts the backend app (index.js) using PM2 so it runs as a service
- Saves the PM2 process list so the app auto‑starts after reboot
- Ensures the backend connects to MongoDB in the private subnet using the generated URI


