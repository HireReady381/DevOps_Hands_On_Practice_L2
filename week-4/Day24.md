# Day 24 — Advanced Terraform Modules and CI/CD Pipeline Scaling

## 🔹 Ansible — Deploy Docker Containers with Kubernetes Using Helm

```yaml
- name: Deploy Docker Containers with Helm
  hosts: localhost
  tasks:
    - name: Install Helm
      command: curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
    - name: Deploy microservices with Helm
      command: helm install HireReady-app ./microservice-chart
🔹 Terraform — Multi-Module Infrastructure with Outputs
module "HireReady_vpc" {
  source = "./vpc"
}

module "HireReady_instance" {
  source = "./instance"
  vpc_id = module.HireReady_vpc.vpc_id
}

output "instance_id" {
  value = module.HireReady_instance.instance_id
}
🔹 Kubernetes — Helm-based Auto-Scaling Deployments
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
---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: HireReady-web-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: HireReady-web-app
  minReplicas: 2
  maxReplicas: 10
  targetCPUUtilizationPercentage: 80
🔹 Jenkinsfile — Scaling CI/CD Pipeline
pipeline {
    agent any
    stages {
        stage('Scale Up') {
            steps {
                echo 'Scaling up Jenkins pipeline agents for heavy workloads...'
                // Simulating scaling logic
            }
        }
        stage('Build') {
            steps {
                echo 'Building project...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying project...'
            }
        }
    }
}
