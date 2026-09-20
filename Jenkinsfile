
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

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    bat 'docker login -u "%DOCKER_USERNAME%" -p "%DOCKER_PASSWORD%"'
                    bat 'docker tag full-cicd-jenkins-docker-aws "%DOCKER_USERNAME%/full-cicd-jenkins-docker-aws:latest"'
                    bat 'docker push "%DOCKER_USERNAME%/full-cicd-jenkins-docker-aws:latest"'
                }
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

