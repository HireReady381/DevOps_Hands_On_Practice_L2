# Day 23 — Serverless Architecture and Microservices
🔹 Ansible — Deploy Lambda Functions Using AWS CLI
- name: Deploy AWS Lambda Function
  hosts: localhost
  tasks:
    - name: Create Lambda function
      command: aws lambda create-function --function-name HireReady-lambda --runtime python3.8 --role arn:aws:iam::123456789012:role/lambda-execution-role --handler lambda_function.lambda_handler --zip-file fileb://lambda.zip
🔹 Terraform — Serverless API Gateway with Lambda Integration
resource "aws_api_gateway_rest_api" "HireReady_api" {
  name        = "HireReady-api"
  description = "HireReady API"
}

resource "aws_lambda_function" "HireReady_lambda" {
  function_name = "HireReady-lambda"
  runtime       = "python3.8"
  role          = aws_iam_role.lambda_exec_role.arn
  handler       = "lambda_function.lambda_handler"
  filename      = "lambda.zip"
}

resource "aws_api_gateway_integration" "lambda_integration" {
  rest_api_id = aws_api_gateway_rest_api.HireReady_api.id
  resource_id = aws_api_gateway_resource.HireReady_resource.id
  http_method = aws_api_gateway_method.HireReady_method.http_method
  integration_http_method = "POST"
  type = "AWS_PROXY"
  uri  = aws_lambda_function.HireReady_lambda.invoke_arn
}
🔹 Kubernetes — Deploy Microservices Using Helm
helm install HireReady-microservice ./microservice-chart
🔹 Jenkinsfile — Deploy Serverless Applications with Lambda
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building Lambda function...'
                sh 'zip -r lambda.zip lambda/'
            }
        }
        stage('Deploy to AWS Lambda') {
            steps {
                script {
                    sh 'aws lambda update-function-code --function-name HireReady-lambda --zip-file fileb://lambda.zip'
                }
            }
        }
    }
}
🔹 GitLab CI/CD — Deploy Lambda Function
stages:
  - build
  - deploy

build:
  stage: build
  script:
    - zip -r lambda.zip lambda/

deploy:
  stage: deploy
  script:
    - aws lambda update-function-code --function-name HireReady-lambda --zip-file fileb://lambda.zip
 
