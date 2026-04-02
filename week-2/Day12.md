Day 12 — Scaling Applications and Automation
🔹 Ansible — Setup Auto-Scaling Group (ASG)
- name: Setup Auto Scaling Group
  hosts: all
  become: yes
  tasks:
    - name: Create Launch Configuration
      ec2_asg:
        name: HireReady-asg
        region: us-east-1
        launch_config_name: HireReady-launch-config
        min_size: 1
        max_size: 3
        desired_capacity: 2
        vpc_zone_identifier: subnet-12345678
🔹 Terraform — Create a Load Balancer with EC2 Instances
resource "aws_lb" "HireReady_lb" {
  name               = "HireReady-lb"
  internal           = false
  load_balancer_type = "application"
  security_groups   = [aws_security_group.allow_ssh_http.id]
  subnets           = [aws_subnet.public.id]
}

resource "aws_instance" "HireReady_ec2" {
  ami           = "ami-0abcd1234abcd1234"
  instance_type = "t2.micro"
  security_groups = [aws_security_group.allow_ssh_http.name]
  tags = {
    Name = "HireReady-EC2"
  }
}
🔹 Kubernetes — Horizontal Pod Autoscaler (HPA) with Load Balancer
apiVersion: apps/v1
kind: Deployment
metadata:
  name: HireReady-web-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: HireReady-web-app
  template:
    metadata:
      labels:
        app: HireReady-web-app
    spec:
      containers:
        - name: web
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
  minReplicas: 1
  maxReplicas: 5
  targetCPUUtilizationPercentage: 80
🔹 Jenkinsfile — Implement Parallel Stages
pipeline {
    agent any
    stages {
        stage('Build & Test') {
            parallel {
                stage('Build') {
                    steps {
                        echo 'Building project...'
                    }
                }
                stage('Test') {
                    steps {
                        echo 'Running tests...'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying project...'
            }
        }
    }
}
🔹 GitLab CI/CD — Parallel Jobs for Build & Test
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - docker build -t HireReady-web-app .

test:
  stage: test
  script:
    - docker run HireReady-web-app npm test

deploy:
  stage: deploy
  script:
    - kubectl apply -f kubernetes/deployment.yaml
