pipeline {
agent any

```
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
                credentialsId: '22635a83-747f-402e-83dc-644c7d1d32c5',
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
            bat 'ping -n 6 127.0.0.1 > nul'
            bat 'curl http://localhost:5001/health'
        }
    }

    stage('Cleanup') {
        steps {
            bat 'docker stop jenkins-test-container'
            bat 'docker rm jenkins-test-container'
        }
    }

    stage('Deploy to EC2') {
        steps {
            withCredentials([sshUserPrivateKey(
                credentialsId: 'ec2-ssh-key',
                keyFileVariable: 'SSH_KEY',
                usernameVariable: 'SSH_USER'
            )]) {

                bat 'ssh -o StrictHostKeyChecking=no -i "%SSH_KEY%" %SSH_USER%@100.54.203.249 "sudo docker pull fazeelmuhammad283/full-cicd-jenkins-docker-aws:latest"'

                bat 'ssh -o StrictHostKeyChecking=no -i "%SSH_KEY%" %SSH_USER%@100.54.203.249 "sudo docker stop project4-container || true"'

                bat 'ssh -o StrictHostKeyChecking=no -i "%SSH_KEY%" %SSH_USER%@100.54.203.249 "sudo docker rm project4-container || true"'

                bat 'ssh -o StrictHostKeyChecking=no -i "%SSH_KEY%" %SSH_USER%@100.54.203.249 "sudo docker run -d --name project4-container -p 5000:5000 fazeelmuhammad283/full-cicd-jenkins-docker-aws:latest"'
            }
        }
    }
}
```

}
