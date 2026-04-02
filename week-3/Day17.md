# Day 17 — Multi-Cloud Deployments, Auto-healing, and Advanced CI/CD

## 🔹 Ansible — Auto-healing Web Servers with Systemd

```yaml
- name: Configure Auto-healing for Web Server
  hosts: all
  become: yes
  tasks:
    - name: Ensure nginx service is running
      service:
        name: nginx
        state: started
        enabled: yes
    - name: Setup auto-restart for nginx service
      lineinfile:
        path: /etc/systemd/system/nginx.service.d/override.conf
        line: "Restart=always"
        create: yes
    - name: Reload systemd
      command: systemctl daemon-reload
🔹 Terraform — Multi-Cloud Infrastructure with AWS and Azure
# AWS VPC
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

# Azure VNet
resource "azurerm_virtual_network" "main" {
  name                = "HireReady-vnet"
  location            = "East US"
  address_space       = ["10.0.0.0/16"]
  resource_group_name = azurerm_resource_group.main.name
}

# EC2 instance in AWS
resource "aws_instance" "HireReady_ec2" {
  ami           = "ami-0abcd1234abcd1234"
  instance_type = "t2.micro"
  region        = "us-east-1"
}

# Virtual Machine in Azure
resource "azurerm_linux_virtual_machine" "HireReady_vm" {
  name                = "HireReady-azure-vm"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  size                = "Standard_B1s"
  admin_username      = "azureuser"
  admin_password      = "P@ssw0rd123!"
}
🔹 Kubernetes — Self-Healing with Liveness and Readiness Probes
apiVersion: apps/v1
kind: Deployment
metadata:
  name: HireReady-web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: HireReady-web-app
  template:
    metadata:
      labels:
        app: HireReady-web-app
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
          livenessProbe:
            httpGet:
              path: /healthz
              port: 80
            initialDelaySeconds: 5
            periodSeconds: 10
          readinessProbe:
            httpGet:
              path: /healthz
              port: 80
            initialDelaySeconds: 5
            periodSeconds: 10
🔹 Jenkinsfile — Rolling Deployments with Kubernetes
pipeline {
    agent any
    environment {
        IMAGE_NAME = 'HireReady-web-app'
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building Docker image...'
                docker.build("${env.IMAGE_NAME}")
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh 'kubectl apply -f kubernetes/deployment.yaml'
                    sh 'kubectl rollout status deployment/HireReady-web-app'
                }
            }
        }
    }
}
🔹 GitLab CI/CD — Multi-Cloud Deployment
stages:
  - build
  - push
  - deploy

build:
  stage: build
  script:
    - docker build -t HireReady-web-app .

push:
  stage: push
  script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD"
    - docker push HireReady-web-app

deploy_aws:
  stage: deploy
  script:
    - kubectl --context=aws-cluster apply -f kubernetes/deployment.yaml

deploy_azure:
  stage: deploy
  script:
    - kubectl --context=azure-cluster apply -f kubernetes/deployment.yaml
 
