pipeline {
    agent { label 'slave' }

    environment {
        AWS_ACCOUNT_ID = "186014908456"
        AWS_REGION = "us-east-1"
        ECR_REPO = "web-dev"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t web-dev .'
            }
        }

        stage('Tag Image') {
            steps {
                sh 'docker tag web-dev:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG'
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION \
                | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                sh 'docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG'
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }
}
