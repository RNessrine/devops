pipeline {
    agent any
    environment {
        SONARQUBE_SERVER = 'sonar-server'
        BACKEND_PROJECT_KEY = 'backend-project'
        FRONTEND_PROJECT_KEY = 'frontend-project'
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
                        script {
                            // Check if Sonar Scanner is installed; if not, install it
                            sh """
                            if ! command -v sonar-scanner &> /dev/null; then
                                echo "Sonar Scanner not found, installing..."
                                curl -o /tmp/sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.7.0.2747-linux.zip
                                unzip /tmp/sonar-scanner.zip -d /opt
                                export PATH=\$PATH:/opt/sonar-scanner-4.7.0.2747-linux/bin
                            fi
                            sonar-scanner \
                            -Dsonar.projectKey=${BACKEND_PROJECT_KEY} \
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

        stage('SonarQube Analysis for Frontend') {
            steps {
                withSonarQubeEnv(SONARQUBE_SERVER) {
                    dir('react-crud-web-api-master') {
                        script {
                            // Ensure Sonar Scanner is available
                            sh """
                            if ! command -v sonar-scanner &> /dev/null; then
                                echo "Sonar Scanner not found, installing..."
                                curl -o /tmp/sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.7.0.2747-linux.zip
                                unzip /tmp/sonar-scanner.zip -d /opt
                                export PATH=\$PATH:/opt/sonar-scanner-4.7.0.2747-linux/bin
                            fi
                            sonar-scanner \
                            -Dsonar.projectKey=${FRONTEND_PROJECT_KEY} \
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
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline exécuté avec succès.'
        }
        failure {
            echo 'Le pipeline a échoué. Vérifiez les logs pour plus d’informations.'
        }
    }
}
