pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
          
                git 'https://github.com/RNessrine/devops.git'
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                script {
                    sh 'docker build -t backend:latest -f Dockerfile.backend .'
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                script {
                    sh 'docker build -t frontend:latest -f Dockerfile.frontend .'
                }
            }
        }
        stage('Deploy with Docker Compose') {
            steps {
                script {
                    sh 'docker-compose -f docker-compose.yml up -d'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
