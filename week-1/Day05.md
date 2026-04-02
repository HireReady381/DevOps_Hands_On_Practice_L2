# Day 05 — Advanced CI/CD and Infrastructure Provisioning

## 🔹 Jenkinsfile — With Git Integration and Testing

```groovy
pipeline {
    agent any
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/HireReady/sample-repo.git'
            }
        }
        stage('Build') {
            steps {
                echo 'Building project from Git repository'
            }
        }
        stage('Test') {
            steps {
                echo 'Running unit tests'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying application'
            }
        }
    }
}
🔹 GitLab CI/CD — With Docker Build
stages:
  - build
  - push
  - deploy

build:
  stage: build
  script:
    - docker build -t HireReady-app .

push:
  stage: push
  script:
    - docker push HireReady-app

deploy:
  stage: deploy
  script:
    - kubectl apply -f kubernetes/deployment.yaml
🔹 Ansible — Create User and Set Permissions
- name: Create a new user
  hosts: all
  become: yes
  tasks:
    - name: Create user
      user:
        name: HireReady
        state: present
        shell: /bin/bash
    - name: Set permissions for the user
      file:
        path: /home/HireReady
        owner: HireReady
        group: HireReady
        mode: '0755'
🔹 Terraform — EC2 with Tags and Volume
resource "aws_instance" "HireReadyEC2" {
  ami           = "ami-0abcd1234abcd1234"
  instance_type = "t2.medium"
  
  tags = {
    Name = "HireReady-Server"
  }

  ebs_block_device {
    device_name = "/dev/sdh"
    volume_size = 50
  }
}
