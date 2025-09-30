pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO = '<account_id>.dkr.ecr.ap-south-1.amazonaws.com/mywebsite'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/<your-user>/<your-repo>.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("mywebsite:${IMAGE_TAG}")
                }
            }
        }

        stage('Login to ECR') {
            steps {
                sh """
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $ECR_REPO
                """
            }
        }

        stage('Tag & Push to ECR') {
            steps {
                sh """
                docker tag mywebsite:${IMAGE_TAG} $ECR_REPO:${IMAGE_TAG}
                docker push $ECR_REPO:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh """
                # Stop old container if exists
                docker ps -q --filter "name=mywebsite" | xargs -r docker stop
                docker ps -aq --filter "name=mywebsite" | xargs -r docker rm

                # Pull and run new container
                docker pull $ECR_REPO:${IMAGE_TAG}
                docker run -d --name mywebsite -p 80:80 $ECR_REPO:${IMAGE_TAG}
                """
            }
        }
    }
}
