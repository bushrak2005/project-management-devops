pipeline {
    agent any

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
                sh 'docker build -t project-management:v1 .'
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
                docker tag project-management:v1 \
                886682669004.dkr.ecr.eu-north-1.amazonaws.com/project-management:v1
                '''
            }
        }
        stage('Push Docker Image') {
            steps {
                echo 'Pushing Docker image to Amazon ECR'

                sh '''
                docker push \
                886682669004.dkr.ecr.eu-north-1.amazonaws.com/project-management:v1
                '''
            }
        }
    }
}