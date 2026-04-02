Day 03 — Ansible, Terraform & Kubernetes Basics
🔹 Ansible — Install Apache and Start Service
- name: Install and Start Apache
  hosts: all
  become: yes
  tasks:
    - name: Install apache
      yum:
        name: httpd
        state: present

    - name: Start apache
      service:
        name: httpd
        state: started
        enabled: yes


🔹 Terraform — EC2 Instance (t3.medium)
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "HireReadyEC2" {
  ami           = "ami-0abcd1234abcd1234"
  instance_type = "t3.medium"
  tags = {
    Name = "HireReady-T3"
  }
}
🔹 Kubernetes — Create a ReplicaSet with 3 Replicas
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: HireReady-replicaset
spec:
  replicas: 3
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
🔹 Shell Script — User Info
#!/bin/bash
echo "Current user: $(whoami)"
echo "Current user groups: $(groups)"
