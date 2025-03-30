pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/sadeepaKD/Tools-Market-CI.git', branch: 'main'
            }
        }
        stage('Build Docker Image') {
            steps {
                bat 'docker build -t accs-market .'
            }
        }
        stage('Run Docker Container') {
            steps {
                bat 'docker stop accs-market || exit 0'
                bat 'docker rm accs-market || exit 0'
                bat 'docker run -d --name accs-market -p 8081:80 accs-market'
            }
        }
    }
    post {
        success {
            echo 'Site is running at http://localhost:8081'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}