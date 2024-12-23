pipeline {
    agent any
    environment {
        // Nom du serveur SonarQube configuré dans Jenkins
        SONARQUBE_SERVER = 'sonar-server'
        // Variables pour l'analyse SonarQube
        BACKEND_PROJECT_KEY = 'backend-project'
        FRONTEND_PROJECT_KEY = 'frontend-project'
    }
    stages {
        stage('Checkout SCM') {
            steps {
                // Clone le dépôt
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
                        sh """
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

        stage('SonarQube Analysis for Frontend') {
            steps {
                withSonarQubeEnv(SONARQUBE_SERVER) {
                    dir('react-crud-web-api-master') {
                        sh """
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

    post {
        always {
            cleanWs() // Nettoie l'espace de travail après chaque exécution
        }
        success {
            echo 'Pipeline exécuté avec succès.'
        }
        failure {
            echo 'Le pipeline a échoué. Vérifiez les logs pour plus d’informations.'
        }
    }
}
