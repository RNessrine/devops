pipeline {
    agent any
    
    tools {
        // Correction du type d'outil SonarQube
        'hudson.plugins.sonar.SonarRunnerInstallation' 'SonarScanner'
    }
    
    environment {
        SONAR_SCANNER_HOME = tool 'SonarScanner'
        PATH = "${env.SONAR_SCANNER_HOME}/bin:${env.PATH}"
    }

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
                withSonarQubeEnv('sonar-server') {
                    dir('nodejs-express-sequelize-mysql-master') {
                        sh """
                            ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                            -Dsonar.projectKey=tuto-backend \
                            -Dsonar.projectName='Backend Tutorial' \
                            -Dsonar.projectVersion=1.0 \
                            -Dsonar.sources=. \
                            -Dsonar.sourceEncoding=UTF-8 \
                            -Dsonar.exclusions=node_modules/**,coverage/**,test/** \
                            -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
                        """
                    }
                }
            }
        }
        
        stage('SonarQube Analysis for Frontend') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    dir('react-crud-web-api-master') {
                        sh """
                            ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                            -Dsonar.projectKey=tuto-frontend \
                            -Dsonar.projectName='Frontend Tutorial' \
                            -Dsonar.projectVersion=1.0 \
                            -Dsonar.sources=. \
                            -Dsonar.sourceEncoding=UTF-8 \
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
        failure {
            echo 'Pipeline failed! Check the logs for details.'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
    }
}
