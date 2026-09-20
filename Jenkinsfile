pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t full-cicd-jenkins-docker-aws .'
            }
        }

        stage('Test Application') {
            steps {
                bat 'docker run -d --name jenkins-test-container -p 5001:5000 full-cicd-jenkins-docker-aws'
                
                bat 'curl http://localhost:5001/health'
            }
        }

        stage('Cleanup') {
            steps {
                bat 'docker stop jenkins-test-container'
                bat 'docker rm jenkins-test-container'
            }
        }
    }
}