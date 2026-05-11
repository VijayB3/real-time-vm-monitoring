# Real-Time VM Monitoring & Automated Email Alerting using Ansible and AWS

## Project Overview

This project is a real-time infrastructure monitoring and automated email alerting system built using Ansible, AWS EC2, Linux, and dynamic inventory.

The system automatically collects health metrics from multiple Linux virtual machines and generates professional HTML monitoring reports which are sent through email notifications.

The project simulates a real-world DevOps monitoring environment where infrastructure health is monitored centrally using automation.

---

## Features

- Real-Time CPU Monitoring
- Memory Usage Monitoring
- Disk Usage Monitoring
- Multi-Server Monitoring
- Dynamic AWS EC2 Inventory
- Automated HTML Email Reports
- SSH Key-Based Authentication
- Infrastructure Health Tracking
- EC2 Instance Tagging Automation
- Centralized Monitoring using Ansible

---

## Technologies Used

- Ansible
- AWS EC2
- Ubuntu Linux
- Dynamic Inventory
- Gmail SMTP
- Shell Scripting
- HTML/CSS
- Jinja2 Templates
- MobaXterm
- Python Virtual Environment

---


# Architecture Diagram

```text
                    +----------------------+
                    |   Ansible Master     |
                    |      (Ubuntu)        |
                    +----------+-----------+
                               |
          ---------------------------------------------
          |           |           |         |         |
          |           |           |         |         |
    +-----+----+ +----+-----+ +---+----+ +--+----+ +-+------+
    | web-01   | | web-02   | | web-03 | | web-04| | web-05 |
    | Ubuntu   | | Ubuntu   | | Ubuntu | | Ubuntu| | Ubuntu |
    +----------+ +----------+ +--------+ +-------+ +--------+

                    |
                    |
                    v

        Collect CPU / Memory / Disk Metrics
                    |
                    v

         Generate Dynamic HTML Report
                    |
                    v

           Send Email Notification
```

---

# Infrastructure Details

| Component | Details |
|---|---|
| Total EC2 Instances | 6 |
| Target Servers Monitored | 5 |
| Ansible Master | 1 Ubuntu Instance |
| Cloud Provider | AWS EC2 |
| Inventory Type | Dynamic Inventory |
| Email Service | Gmail SMTP |

---

# Project Structure

```bash
├── group_vars
├── inventory
├── templates
├── Ansible Project To Monitor VMs Health Steps Doc.docx
├── ansible.cfg
├── collect_metrics.yaml
├── playbook.yaml
└── send_report.yaml
```

---

# Dynamic Inventory Configuration

## inventory/aws_ec2.yaml

```yaml
plugin: amazon.aws.aws_ec2

regions:
  - ap-south-1

filters:
  tag:Environment: dev
  instance-state-name: running

compose:
  ansible_host: public_ip_address

keyed_groups:
  - key: tags.Name
    prefix: name

  - key: tags.Environment
    prefix: env
```

---

# Ansible Configuration

## ansible.cfg

```ini
[defaults]
inventory = ./inventory/aws_ec2.yaml
host_key_checking = False

[ssh_connection]
ssh_args = -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null
```

---

# Step-by-Step Setup

## Step 1: Update Ubuntu System

```bash
sudo apt update && sudo apt upgrade -y
```

---

## Step 2: Add Ansible PPA

```bash
sudo add-apt-repository --yes --update ppa:ansible/ansible
```

---

## Step 3: Install Ansible

```bash
sudo apt install ansible -y
```

---

## Step 4: Install AWS CLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

sudo apt install unzip

unzip awscliv2.zip

sudo ./aws/install
```

---

## Step 5: Configure AWS CLI

```bash
aws configure
```

---

## Step 6: Create Python Virtual Environment

```bash
sudo apt install python3-venv -y

python3 -m venv ansible-env

source ansible-env/bin/activate
```

---

## Step 7: Install Required Python Packages

```bash
pip install boto3 botocore docker
```

---

## Step 8: Install AWS Ansible Collection

```bash
ansible-galaxy collection install amazon.aws
```

---

## Step 9: Verify Dynamic Inventory

```bash
ansible-inventory -i inventory/aws_ec2.yaml --graph
```

---

# EC2 Tagging Automation Script

```bash
#!/bin/bash

instance_ids=$(aws ec2 describe-instances \
  --filters "Name=tag:Environment,Values=dev" \
  "Name=instance-state-name,Values=running" \
  --query 'Reservations[*].Instances[*].InstanceId' \
  --output text)

sorted_ids=($(echo "$instance_ids" | tr '\t' '\n' | sort))

counter=1

for id in "${sorted_ids[@]}"; do

  name="web-$(printf "%02d" $counter)"

  echo "Tagging $id as $name"

  aws ec2 create-tags --resources "$id" \
    --tags Key=Name,Value="$name"

  ((counter++))

done
```

---

# SSH Public Key Injection Script

```bash
#!/bin/bash

PEM_FILE="DevOps-Shack.pem"

PUB_KEY=$(cat ~/.ssh/id_rsa.pub)

USER="ubuntu"

INVENTORY_FILE="inventory/aws_ec2.yaml"

HOSTS=$(ansible-inventory -i $INVENTORY_FILE --list | jq -r '._meta.hostvars | keys[]')

for HOST in $HOSTS; do

  echo "Injecting key into $HOST"

  ssh -o StrictHostKeyChecking=no -i $PEM_FILE $USER@$HOST "

    mkdir -p ~/.ssh && \

    echo \"$PUB_KEY\" >> ~/.ssh/authorized_keys && \

    chmod 700 ~/.ssh && \

    chmod 600 ~/.ssh/authorized_keys

  "

done
```

---

# Monitoring Metrics Collected

The system collects the following metrics from all monitored Linux servers:

- CPU Usage
- Memory Usage
- Disk Usage
- Hostname
- Server Health Status

---

# Workflow

1. Launch AWS EC2 Instances
2. Configure Dynamic Inventory
3. Setup SSH Authentication
4. Execute Ansible Playbooks
5. Collect Real-Time VM Metrics
6. Generate Dynamic HTML Reports
7. Send Email Notifications

---

# Execute the Project

```bash
ansible-playbook playbook.yaml
```

---

# Sample Monitoring Report

The HTML report includes:

- Server Name
- CPU Usage
- Memory Usage
- Disk Usage
- Health Status
- Warning & Critical Alerts

---

# Screenshots

## AWS EC2 Infrastructure
 
<img width="1920" height="960" alt="3" src="https://github.com/user-attachments/assets/8d13cd10-789a-4c5b-a9f5-1a1b805ca64a" />


---

## MobaXterm SSH Connection
 
<img width="1920" height="1080" alt="Screenshot (101)" src="https://github.com/user-attachments/assets/ee5d334b-5aa7-42da-bdea-aab3a1675e83" />

---

## Dynamic Inventory Output
 
<img width="1920" height="1080" alt="Screenshot (91)" src="https://github.com/user-attachments/assets/3dc92ffd-96d3-44e9-b366-800d71856a04" />

---

## Ansible Playbook Execution
 
<img width="1920" height="1024" alt="5" src="https://github.com/user-attachments/assets/d32667f7-4cbd-48e9-a3d7-09e9c6752b67" />

---

## HTML Monitoring Dashboard
 
<img width="1920" height="975" alt="1" src="https://github.com/user-attachments/assets/3d7400b6-b2e6-44ab-b9ec-08264867c09a" />

<img width="1920" height="971" alt="2" src="https://github.com/user-attachments/assets/4827f976-1125-42a5-bc0d-bfb6b52ef219" />


---

# Future Enhancements

- Grafana Dashboard Integration
- Prometheus Monitoring
- Slack Notifications
- CloudWatch Integration
- Auto Scaling Alerts
- Kubernetes Monitoring

---

# Learning Outcomes

This project helped in gaining hands-on experience with:

- DevOps Automation
- Infrastructure Monitoring
- Configuration Management
- Linux Administration
- AWS EC2 Management
- Dynamic Inventory
- Shell Scripting
- Email Automation

---

# Repository

GitHub Repository:

https://github.com/VijayB3/real-time-vm-monitoring

---

# Author

Vijay B

---

# Connect

If you found this project useful, feel free to star the repository and connect on LinkedIn.
