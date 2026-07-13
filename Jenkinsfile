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

    }
}