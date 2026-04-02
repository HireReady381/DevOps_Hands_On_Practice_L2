# Day 18 — High Availability, Multi-Region, and Auto-scaling

## 🔹 Ansible — Configure HAProxy for Load Balancing

```yaml
- name: Setup HAProxy Load Balancer
  hosts: all
  become: yes
  tasks:
    - name: Install HAProxy
      yum:
        name: haproxy
        state: present
    - name: Configure HAProxy
      template:
        src: haproxy.cfg.j2
        dest: /etc/haproxy/haproxy.cfg
    - name: Start HAProxy service
      service:
        name: haproxy
        state: started
        enabled: yes
🔹 Terraform — Multi-Region Auto-scaling Group with ALB
resource "aws_lb" "HireReady_lb" {
  name               = "HireReady-lb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.allow_ssh_http.id]
  subnets            = [aws_subnet.public.id, aws_subnet.public2.id]
}

resource "aws_autoscaling_group" "HireReady_asg" {
  desired_capacity     = 3
  max_size             = 5
  min_size             = 2
  vpc_zone_identifier  = [aws_subnet.public.id, aws_subnet.public2.id]
  launch_configuration = aws_launch_configuration.HireReady_config.id
}
🔹 Kubernetes — Horizontal Pod Autoscaler with Multiple Replicas
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
🔹 Jenkinsfile — Blue-Green Deployment Strategy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building Docker image...'
                docker.build('HireReady-web-app')
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }
        stage('Blue-Green Deployment') {
            steps {
                script {
                    sh 'kubectl apply -f kubernetes/blue-deployment.yaml'
                    sh 'kubectl apply -f kubernetes/green-deployment.yaml'
                    sh 'kubectl rollout status deployment/blue-deployment'
                    sh 'kubectl rollout status deployment/green-deployment'
                }
            }
        }
    }
}
🔹 GitLab CI/CD — Blue-Green Deployment with Kubernetes
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

deploy:
  stage: deploy
  script:
    - kubectl apply -f kubernetes/blue-deployment.yaml
    - kubectl apply -f kubernetes/green-deployment.yaml
    - kubectl rollout status deployment/blue-deployment
    - kubectl rollout status deployment/green-deployment

