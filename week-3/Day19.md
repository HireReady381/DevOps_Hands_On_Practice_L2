Day 19 — Advanced Monitoring, Logging, and Security
🔹 Ansible — Setup Prometheus for Monitoring
- name: Install Prometheus for Monitoring
  hosts: all
  become: yes
  tasks:
    - name: Install Prometheus dependencies
      yum:
        name:
          - prometheus
          - prometheus-node-exporter
        state: present
    - name: Start Prometheus service
      service:
        name: prometheus
        state: started
        enabled: yes
🔹 Terraform — Setup AWS CloudWatch for Monitoring
resource "aws_cloudwatch_log_group" "HireReady_log_group" {
  name = "HireReady-log-group"
}

resource "aws_cloudwatch_log_stream" "HireReady_log_stream" {
  log_group_name = aws_cloudwatch_log_group.HireReady_log_group.name
  name           = "HireReady-log-stream"
}
🔹 Kubernetes — Deploy Fluentd for Logging
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fluentd
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      containers:
        - name: fluentd
          image: fluent/fluentd
          ports:
            - containerPort: 24224
---
apiVersion: v1
kind: Service
metadata:
  name: fluentd
spec:
  selector:
    app: fluentd
  ports:
    - protocol: TCP
      port: 24224
🔹 Jenkinsfile — Integrating Prometheus Monitoring in Pipeline
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
        stage('Monitor Deployment') {
            steps {
                script {
                    sh 'kubectl apply -f kubernetes/deployment.yaml'
                    sh 'kubectl get pods --watch'
                }
            }
        }
    }
}
🔹 GitLab CI/CD — Prometheus and Logging Integration
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
    - kubectl apply -f kubernetes/deployment.yaml
    - kubectl get pods --watch
