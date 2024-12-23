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
                dir('nodejs-express-sequelize-mysql-master') {
                    sh 'docker build -t backend:latest .'
                }
            }
        }
        stage('Build Frontend') {
            steps {
                dir('react-crud-web-api-master') {
                    sh 'docker build -t frontend:latest .'
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deployment steps go here'
            }
        }
        stage('SonarQube Analysis for Backend') {
            steps {
                withSonarQubeEnv('sonar-server') { // Remplacez 'sonar-server' par le nom exact de votre configuration SonarQube
                    dir('nodejs-express-sequelize-mysql-master') {
                        sh """
                            sonar-scanner \
                            -Dsonar.projectKey=tuto-backend \
                            -Dsonar.sources=. \
                            -Dsonar.exclusions=node_modules/,coverage/,test/ \
                            -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
                        """
                    }
                }
            }
        }
        stage('SonarQube Analysis for Frontend') {
            steps {
                withSonarQubeEnv('sonar-server') { // Remplacez 'sonar-server' par le nom exact de votre configuration SonarQube
                    dir('react-crud-web-api-master') {
                        sh """
                            sonar-scanner \
                            -Dsonar.projectKey=tuto-frontend \
                            -Dsonar.sources=. \
                            -Dsonar.exclusions=node_modules/,coverage/,test/ \
                            -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
                        """
                    }
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
