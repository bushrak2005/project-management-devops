pipeline {
    agent any

    options {
        disableConcurrentBuilds()
    }
    environment {
        AWS_ACCOUNT_ID = "886682669004"
        AWS_REGION = "eu-north-1"
        IMAGE_NAME = "project-management"

        ECR_REPOSITORY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${IMAGE_NAME}"

        APP_SERVER_IP = "172.31.40.158"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checkout stage started'
                sh 'pwd'
                sh 'ls -la'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Cleaning old dependencies'

                sh 'rm -rf node_modules'

                sh 'rm -f package-lock.json'

                echo 'Installing fresh dependencies'

                sh 'npm install'
            }
        }
        stage('Build React App') {
            steps {
                echo 'Building React application'
                sh 'npm run build'
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image'
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }
        stage('Login to Amazon ECR') {
            steps {
                echo 'Logging in to Amazon ECR'

                sh '''
                aws ecr get-login-password --region eu-north-1 | \
                docker login --username AWS --password-stdin 886682669004.dkr.ecr.eu-north-1.amazonaws.com
                '''
            }
        }
        stage('Tag Docker Image') {
            steps {
                echo 'Tagging Docker image'

                sh '''
                docker tag \
                $IMAGE_NAME:$BUILD_NUMBER \
                $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$IMAGE_NAME:$BUILD_NUMBER

                docker tag \
                $IMAGE_NAME:$BUILD_NUMBER \
                $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$IMAGE_NAME:latest
                '''
            }
        }
        stage('Push Docker Image') {
            steps {
                echo 'Pushing Docker image to Amazon ECR'

                sh '''
                docker push \
                $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$IMAGE_NAME:$BUILD_NUMBER

                docker push \
                $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$IMAGE_NAME:latest
                '''
            }
        }
        stage('Deploy Application') {
            steps {
               echo 'Deploying application to App Server'

                sh """
                ssh -o StrictHostKeyChecking=no ubuntu@${APP_SERVER_IP} '
                aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com

                docker pull ${ECR_REPOSITORY}:latest

                docker stop project-management-container || true

                docker rm project-management-container || true

                docker run -d \
                --name project-management-container \
                -p 80:80 \
                ${ECR_REPOSITORY}:latest

                docker ps
                '
                """
            }
        }
    }
}