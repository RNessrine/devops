pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/RNessrine/devops.git'
            }
        }
        stage('Build Backend') {
            steps {
                dir('nodejs-express-sequelize-mysql-master') { // Spécifiez le répertoire contenant le Dockerfile du backend
                    sh 'docker build -t backend:latest .'
                }
            }
        }
        stage('Build Frontend') {
            steps {
                dir('react-crud-web-api-master') { // Spécifiez le répertoire contenant le Dockerfile du frontend
                    sh 'docker build -t frontend:latest .'
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deployment steps go here'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
