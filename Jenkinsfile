pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO = "477654738615.dkr.ecr.us-east-1.amazonaws.com/html-website"
        IMAGE_TAG = "latest"
        CLUSTER = "html-website-cluster"
        SERVICE = "html-website-service"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t html-website ./app'
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $ECR_REPO
                '''
            }
        }

        stage('Tag Image') {
            steps {
                sh '''
                docker tag html-website:latest $ECR_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                docker push $ECR_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Deploy to ECS') {
            steps {
                sh '''
                aws ecs update-service \
                  --cluster $CLUSTER \
                  --service $SERVICE \
                  --force-new-deployment \
                  --region $AWS_REGION
                '''
            }
        }
    }
}
