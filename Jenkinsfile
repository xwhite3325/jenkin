pipeline {
    agent any
    environment {
        AWS_REGION = "ap-south-1"
        ECR_REPO = "204625684000.dkr.ecr.ap-south-1.amazonaws.com/mywebsite"
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/xwhite3325/jenkin.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t mywebsite .'
            }
        }
        stage('Login to ECR') {
            steps {
                sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO'
            }
        }
        stage('Tag & Push to ECR') {
            steps {
                sh 'docker tag mywebsite:latest $ECR_REPO:latest'
                sh 'docker push $ECR_REPO:latest'
            }
        }
    }
}
