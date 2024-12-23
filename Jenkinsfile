pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'sonar-server' // Nom configuré dans Jenkins
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
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

        stage('SonarQube Analysis for Backend') {
            steps {
                withSonarQubeEnv(SONARQUBE_SERVER) {
                    dir('nodejs-express-sequelize-mysql-master') {
                        sh '''
                            sonar-scanner \
                            -Dsonar.projectKey=backend-project \
                            -Dsonar.sources=. \
                            -Dsonar.sourceEncoding=UTF-8 \
                            -Dsonar.exclusions=node_modules/**,coverage/**,test/** \
                            -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
                        '''
                    }
                }
            }
        }

        stage('SonarQube Analysis for Frontend') {
            steps {
                withSonarQubeEnv(SONARQUBE_SERVER) {
                    dir('react-crud-web-api-master') {
                        sh '''
                            sonar-scanner \
                            -Dsonar.projectKey=frontend-project \
                            -Dsonar.sources=. \
                            -Dsonar.sourceEncoding=UTF-8 \
                            -Dsonar.exclusions=node_modules/**,coverage/**,test/** \
                            -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
                        '''
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
