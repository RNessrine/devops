pipeline {
    agent any

    environment {
        // Nom de l'instance SonarQube configurée dans Jenkins
        //SONARQUBE_SERVER = 'sonar-server'
        //SONAR_SCANNER_HOME = tool 'SonarQube'
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "35.173.178.243:8088"
        NEXUS_REPOSITORY = "maven-nexus-repo"
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
                    // Lire le fichier pom.xml
                    def pom = readMavenPom file: "pom.xml"

                    // Rechercher les fichiers correspondants dans le dossier target
                    def files = findFiles(glob: "target/*.${pom.packaging}")

                    if (files.size() > 0) {
                        def artifactPath = files[0].path
                        def artifactExists = fileExists artifactPath

                        if (artifactExists) {
                            echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version: ${pom.version}"

                            // Télécharger l'artifact et le fichier pom.xml vers Nexus
                            nexusArtifactUploader(
                                nexusVersion: NEXUS_VERSION,
                                protocol: NEXUS_PROTOCOL,
                                nexusUrl: NEXUS_URL,
                                repository: NEXUS_REPOSITORY,
                                credentialsId: NEXUS_CREDENTIAL_ID,
                                groupId: pom.groupId,
                                version: pom.version,
                                artifacts: [
                                    [
                                        artifactId: pom.artifactId,
                                        classifier: '',
                                        file: artifactPath,
                                        type: pom.packaging
                                    ],
                                    [
                                        artifactId: pom.artifactId,
                                        classifier: '',
                                        file: "pom.xml",
                                        type: "pom"
                                    ]
                                ]
                            )
                        } else {
                            error "*** File: ${artifactPath}, could not be found"
                        }
                    } else {
                        error "No artifact found in target directory."
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
