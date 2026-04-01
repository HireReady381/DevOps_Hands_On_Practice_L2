# Day 29 — Final Project Setup and Configuration

## 🔹 Ansible — Complete Deployment Setup

```yaml
- name: Complete Deployment Setup
  hosts: all
  become: yes
  tasks:
    - name: Deploy Nginx, MySQL, and Redis with Docker
      command: docker-compose -f /path/to/docker-compose.yml up -d
      
🔹 Terraform — Provisioning Multi-Tier Application with VPC and Security
resource "aws_vpc" "pathnex_vpc" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_instance" "pathnex_app" {
  ami           = "ami-0abcd1234abcd1234"
  instance_type = "t2.medium"
  subnet_id     = aws_subnet.pathnex_subnet.id
  security_groups = [aws_security_group.pathnex_sg.name]
}
🔹 Kubernetes — Deploy Full Application Stack
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pathnex-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: pathnex-app
  template:
    metadata:
      labels:
        app: pathnex-app
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: pathnex-service
spec:
  selector:
    app: pathnex-app
  ports:
    - port: 80
      targetPort: 80
