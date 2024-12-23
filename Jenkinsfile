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
        stage ('SonarQube Analysis for backend') {
            steps {
                withSonarQubeEnv('sonar-qube-server') {
                    dir("nodejs-express-sequelize-mysql-master") {
                        sh """
                            ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                            -Dsonar.projectKey=tuto-backend \
                            -Dsonar.sources=. \
                            -Dsonar.exclusions=node_modules/**,coverage/**,test/** \
                            -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
                            """
                    }
                }
            }
        }
        stage ('SonarQube Analysis for frontend') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    dir("frontend") {
                        sh """
                            ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                            -Dsonar.projectKey=tuto-frontend \
                            -Dsonar.sources=. \
                            -Dsonar.exclusions=node_modules/**,coverage/**,test/** \
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
