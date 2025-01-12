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
                        // Create a tarball of the backend
                        sh 'tar -czf backend.tar.gz --exclude="node_modules" --exclude=".git" .'
                        
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
                        // Create a tarball of the frontend
                        sh 'tar -czf frontend.tar.gz --exclude="node_modules" --exclude=".git" .'
                        
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
