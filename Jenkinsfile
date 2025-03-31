pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
        IMAGE_NAME = "sadeepakd/tools-market-ci"
        DROPLET_IP = "143.110.236.166" // Replace with your production Droplet IP
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/sadeepaKD/Tools-Market-CI.git', branch: 'main'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${env.BUILD_NUMBER}")
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        docker.image("${IMAGE_NAME}:${env.BUILD_NUMBER}").push()
                        docker.image("${IMAGE_NAME}:${env.BUILD_NUMBER}").push('latest')
                    }
                }
            }
        }
        stage('Deploy to Production') {
            steps {
                sshagent(['droplet-ssh-credentials']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no root@${DROPLET_IP} '
                        docker stop tools-market-ci || true &&
                        docker rm tools-market-ci || true &&
                        docker pull ${IMAGE_NAME}:latest &&
                        docker run -d --name tools-market-ci -p 80:80 --restart unless-stopped ${IMAGE_NAME}:latest
                    '
                    """
                }
            }
        }
    }
    post {
        failure {
            echo "Pipeline failed."
        }
        success {
            echo "Deployment successful!"
        }
    }
}  