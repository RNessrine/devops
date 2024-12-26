pipeline {
    agent any

    environment {
        // Nom de l'instance SonarQube configurée dans Jenkins
        SONARQUBE_SERVER = 'sonar-server'
        SONAR_SCANNER_HOME = tool 'SonarQube'
      //  NEXUS_VERSION = "nexus3"
        //NEXUS_PROTOCOL = "http"
       // NEXUS_URL = "35.173.178.243:8081"
        ///NEXUS_REPOSITORY = "maven-nexus-repo"
        //NEXUS_CREDENTIAL_ID = "nexus-user-credentials"
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

        stage('Deploy') {
            steps {
                echo 'Deployment steps go here'
            }
        }

        stage('SonarQube Analysis for Backend') {
            steps {
                withSonarQubeEnv(SONARQUBE_SERVER) {
                    dir('nodejs-express-sequelize-mysql-master') {
                        sh '''
                        ${SONAR_SCANNER_HOME}/bin/sonar-scanner\
                            -Dsonar.projectKey=backend-project \
                            -Dsonar.projectName="Backend Project" \
                            -Dsonar.projectVersion=1.0 \
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
                        ${SONAR_SCANNER_HOME}/bin/sonar-scanner\
                            -Dsonar.projectKey=frontend-project \
                            -Dsonar.projectName="Frontend Project" \
                            -Dsonar.projectVersion=1.0 \
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
