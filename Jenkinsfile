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
                echo 'Installing Node.js dependencies'
                sh 'npm install'
            }
        }

    }
}