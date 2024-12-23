
       pipeline {
    agent any
    
    environment {
        DOCKER_COMPOSE_VERSION = '1.29.2'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'main']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    submoduleCfg: [],
                    userRemoteConfigs: [[
                        url: 'https://github.com/RNessrine/devops.git'
                    ]]
                ])
            }
        }
        
        stage('Build Backend Docker Image') {
            steps {
                dir('backend') {
                    sh 'docker build -t backend:latest .'
                }
            }
        }
        
        stage('Build Frontend Docker Image') {
            steps {
                dir('frontend') {
                    sh 'docker build -t frontend:latest .'
                }
            }
        }
        
        stage('Deploy with Docker Compose') {
            steps {
                sh 'docker-compose up -d'
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}
