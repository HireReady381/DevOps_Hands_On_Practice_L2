Day 02 — Services & Tags

🔹 Ansible — Install & Enable Nginx
- name: Install and start Nginx on HireReady
  hosts: all
  become: yes

  tasks:
    - name: Install nginx
      yum:
        name: nginx
        state: present

    - name: Enable nginx
      service:
        name: nginx
        state: started
        enabled: yes


🔹 Terraform — EC2 with Tags (r5.2xlarge)
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "HireReadyEC2" {
  ami           = "ami-0abcd1234abcd1234"
  instance_type = "r5.2xlarge"

  tags = {
    Name        = "HireReady-Server"
    Environment = "Training"
    Owner       = "HireReadyStudent"
  }
}


# Kubernetes — Deployment with 2 Replicas
apiVersion: apps/v1
kind: Deployment
metadata:
  name: HireReady-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: HireReady-app
  template:
    metadata:
      labels:
        app: HireReady-app
    spec:
      containers:
        - name: app
          image: nginx
          ports:
            - containerPort: 80


🔹 Shell Script — Disk Usage
#!/bin/bash
df -h


