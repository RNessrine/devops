pipeline {
    agent any

    environment {
        // Nom de l'instance SonarQube configurée dans Jenkins
        //SONARQUBE_SERVER = 'sonar-server'
        //SONAR_SCANNER_HOME = tool 'SonarQube'
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "35.173.178.243:8088"
        NEXUS_REPOSITORY = "npm-hosted"
        NEXUS_CREDENTIAL_ID = "nexus-user-credentials"
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

        // Étape d'analyse SonarQube pour le backend
        // stage('SonarQube Analysis for Backend') {
        //     steps {
        //         withSonarQubeEnv(SONARQUBE_SERVER) {
        //             dir('nodejs-express-sequelize-mysql-master') {
        //                 sh '''
        //                 ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
        //                     -Dsonar.projectKey=backend-project \
        //                     -Dsonar.projectName="Backend Project" \
        //                     -Dsonar.projectVersion=1.0 \
        //                     -Dsonar.sources=. \
        //                     -Dsonar.sourceEncoding=UTF-8 \
        //                     -Dsonar.exclusions=node_modules/**,coverage/**,test/** \
        //                     -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
        //                 '''
        //             }
        //         }
        //     }
        // }

        // Étape d'analyse SonarQube pour le frontend
        // stage('SonarQube Analysis for Frontend') {
        //     steps {
        //         withSonarQubeEnv(SONARQUBE_SERVER) {
        //             dir('react-crud-web-api-master') {
        //                 sh '''
        //                 ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
        //                     -Dsonar.projectKey=frontend-project \
        //                     -Dsonar.projectName="Frontend Project" \
        //                     -Dsonar.projectVersion=1.0 \
        //                     -Dsonar.sources=. \
        //                     -Dsonar.sourceEncoding=UTF-8 \
        //                     -Dsonar.exclusions=node_modules/**,coverage/**,test/** \
        //                     -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
        //                 '''
        //             }
        //         }
        //     }
        // }

        stage('Upload Artifact to Nexus') {
            steps {
                script {
                    // Upload Backend artifacts
                    dir('nodejs-express-sequelize-mysql-master') {
                        sh '''
                            # Créer un répertoire temporaire
                            mkdir -p temp_backend
                            # Copier les fichiers nécessaires
                            cp -r * temp_backend/ || true
                            # Exclure node_modules et .git
                            rm -rf temp_backend/node_modules temp_backend/.git || true
                            # Créer l'archive
                            tar -czf backend.tar.gz -C temp_backend .
                            # Nettoyer
                            rm -rf temp_backend
                        '''
                        
                        // Upload to Nexus
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            groupId: 'com.devops',
                            version: env.BUILD_NUMBER,
                            artifacts: [
                                [artifactId: 'backend',
                                 classifier: '',
                                 file: 'backend.tar.gz',
                                 type: 'tar.gz']
                            ]
                        )
                    }

                    // Upload Frontend artifacts
                    dir('react-crud-web-api-master') {
                        sh '''
                            # Créer un répertoire temporaire
                            mkdir -p temp_frontend
                            # Copier les fichiers nécessaires
                            cp -r * temp_frontend/ || true
                            # Exclure node_modules et .git
                            rm -rf temp_frontend/node_modules temp_frontend/.git || true
                            # Créer l'archive
                            tar -czf frontend.tar.gz -C temp_frontend .
                            # Nettoyer
                            rm -rf temp_frontend
                        '''
                        
                        // Upload to Nexus
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            groupId: 'com.devops',
                            version: env.BUILD_NUMBER,
                            artifacts: [
                                [artifactId: 'frontend',
                                 classifier: '',
                                 file: 'frontend.tar.gz',
                                 type: 'tar.gz']
                            ]
                        )
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
