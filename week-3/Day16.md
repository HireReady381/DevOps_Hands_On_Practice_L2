Day 16 — Advanced CloudFormation & Multi-Region Deployments
🔹 Ansible — Create and Start EC2 Instances
- name: Create EC2 instances
  hosts: localhost
  become: yes
  tasks:
    - name: Launch EC2 instance in us-east-1
      ec2_instance:
        name: HireReady-ec2-us-east-1
        key_name: HireReady-key
        region: us-east-1
        instance_type: t2.micro
        image: ami-0abcd1234abcd1234
        wait: yes
    - name: Launch EC2 instance in us-west-1
      ec2_instance:
        name: HireReady-ec2-us-west-1
        key_name: HireReady-key
        region: us-west-1
        instance_type: t2.micro
        image: ami-0abcd1234abcd1234
        wait: yes
🔹 Terraform — Multi-Region VPC with EC2
resource "aws_vpc" "us_east_vpc" {
  cidr_block = "10.0.0.0/16"
  region     = "us-east-1"
}

resource "aws_vpc" "us_west_vpc" {
  cidr_block = "10.1.0.0/16"
  region     = "us-west-1"
}

resource "aws_instance" "HireReady_ec2_us_east" {
  ami           = "ami-0abcd1234abcd1234"
  instance_type = "t2.micro"
  region        = "us-east-1"
}

resource "aws_instance" "HireReady_ec2_us_west" {
  ami           = "ami-0abcd1234abcd1234"
  instance_type = "t2.micro"
  region        = "us-west-1"
}
🔹 Kubernetes — Multi-Cluster Deployment with Federation
apiVersion: apps/v1
kind: Deployment
metadata:
  name: HireReady-deployment
  labels:
    app: HireReady-app
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
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80

---

apiVersion: v1
kind: Service
metadata:
  name: HireReady-service
spec:
  selector:
    app: HireReady-app
  ports:
    - protocol: TCP
      port: 80
🔹 Jenkinsfile — Deploy to Multi-Cluster Environments
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
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    if (env.CLUSTER == 'us-east') {
                        sh 'kubectl --context=us-east apply -f kubernetes/deployment.yaml'
                    } else {
                        sh 'kubectl --context=us-west apply -f kubernetes/deployment.yaml'
                    }
                }
            }
        }
    }
}
🔹 GitLab CI/CD — Multi-Region Deployment with Kubernetes
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
    - |
      if [ "$CI_COMMIT_REF_NAME" == "main" ]; then
        kubectl --context=us-east apply -f kubernetes/prod-deployment.yaml
      else
        kubectl --context=us-west apply -f kubernetes/dev-deployment.yaml
      fi
