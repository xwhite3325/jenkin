pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'  // change if needed
        ECR_REPO = '123456789012.dkr.ecr.ap-south-1.amazonaws.com/mywebsite' // replace with your ECR repo
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh """
                    docker build -t mywebsite:${IMAGE_TAG} .
                """
            }
        }

        stage('Login to ECR') {
            steps {
                echo "Logging in to AWS ECR..."
                sh """
                    aws ecr get-login-password --region $AWS_REGION | \
                    docker login --username AWS --password-stdin $ECR_REPO
                """
            }
        }

        stage('Tag & Push to ECR') {
            steps {
                echo "Tagging and pushing image to ECR..."
                sh """
                    docker tag mywebsite:${IMAGE_TAG} $ECR_REPO:${IMAGE_TAG}
                    docker push $ECR_REPO:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo "Deploying Docker container..."
                sh """
                    # Stop and remove old container if exists
                    docker ps -q --filter "name=mywebsite" | xargs -r docker stop
                    docker ps -aq --filter "name=mywebsite" | xargs -r docker rm

                    # Pull latest image and run
                    docker pull $ECR_REPO:${IMAGE_TAG}
                    docker run -d --name mywebsite -p 80:80 $ECR_REPO:${IMAGE_TAG}
                """
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check logs."
        }
    }
}
