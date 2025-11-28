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



### Application 

<img width="1609" height="1061" alt="Screenshot 2025-11-28 111651" src="https://github.com/user-attachments/assets/1c2bb0c9-c010-4f13-8961-810d38b61f1c" />



<img width="1432" height="986" alt="Screenshot 2025-11-28 113724" src="https://github.com/user-attachments/assets/13477307-b2d8-4940-ad81-14d14b2f85c0" />



<img width="1812" height="722" alt="Screenshot 2025-11-28 113926" src="https://github.com/user-attachments/assets/916247f8-2ce6-4beb-b7a9-1a0702f9a828" />

### Database





<img width="1619" height="973" alt="Screenshot 2025-11-28 135615" src="https://github.com/user-attachments/assets/0a80dda9-0c7f-49fe-8c15-ba317404a975" />



---

### Security hardening


<img width="770" height="344" alt="Screenshot 2025-11-28 113849" src="https://github.com/user-attachments/assets/a203343e-a57a-4c41-aace-46965a385560" />


<img width="770" height="340" alt="Screenshot 2025-11-28 112518" src="https://github.com/user-attachments/assets/d3fead1c-cc2b-4fd6-96b6-dc8f814c9470" />





