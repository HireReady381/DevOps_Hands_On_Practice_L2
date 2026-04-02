Day 22 — Multi-Cluster Kubernetes Management
🔹 Ansible — Deploy Nginx Across Multiple Kubernetes Clusters
- name: Deploy Nginx across multiple Kubernetes clusters
  hosts: localhost
  tasks:
    - name: Configure kubectl for cluster 1
      command: kubectl --context=us-east config use-context us-east-cluster
    - name: Deploy Nginx on Cluster 1
      command: kubectl apply -f kubernetes/deployment.yaml
    - name: Configure kubectl for cluster 2
      command: kubectl --context=us-west config use-context us-west-cluster
    - name: Deploy Nginx on Cluster 2
      command: kubectl apply -f kubernetes/deployment.yaml
🔹 Terraform — Multi-Region Kubernetes Cluster with EKS and GKE
resource "aws_eks_cluster" "HireReady_eks" {
  name     = "HireReady-eks-cluster"
  role_arn = aws_iam_role.eks_cluster_role.arn

  vpc_config {
    subnet_ids = [aws_subnet.HireReady_subnet.id]
  }

  depends_on = [aws_iam_role_policy_attachment.eks_cluster_role_policy]
}

resource "google_container_cluster" "HireReady_gke" {
  name     = "HireReady-gke-cluster"
  location = "us-central1-a"
  initial_node_count = 3
  node_config {
    machine_type = "n1-standard-1"
  }
}
🔹 Kubernetes — Federated Deployment with Multiple Clusters
apiVersion: apps/v1
kind: Deployment
metadata:
  name: HireReady-deployment
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
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
---
apiVersion: federation.k8s.io/v1beta1
kind: FederatedDeployment
metadata:
  name: HireReady-federated-deployment
  namespace: default
spec:
  template:
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
            - name: nginx
              image: nginx
              ports:
                - containerPort: 80
🔹 Jenkinsfile — Multi-Cluster Deployment Strategy
pipeline {
    agent any
    stages {
        stage('Deploy to Cluster 1') {
            steps {
                script {
                    sh 'kubectl --context=us-east-cluster apply -f kubernetes/deployment.yaml'
                }
            }
        }
        stage('Deploy to Cluster 2') {
            steps {
                script {
                    sh 'kubectl --context=us-west-cluster apply -f kubernetes/deployment.yaml'
                }
            }
        }
    }
}
🔹 GitLab CI/CD — Multi-Cluster Deployment Strategy
stages:
  - deploy

deploy_cluster_1:
  stage: deploy
  script:
    - kubectl --context=us-east-cluster apply -f kubernetes/deployment.yaml

deploy_cluster_2:
  stage: deploy
  script:
    - kubectl --context=us-west-cluster apply -f kubernetes/deployment.yaml

