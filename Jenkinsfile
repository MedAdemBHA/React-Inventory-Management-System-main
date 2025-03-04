pipeline {
    agent any
    environment {
        DOCKER_CREDENTIALS_ID = 'docker-hub'
        DOCKER_IMAGE_FRONTEND = 'react-frontend'
        DOCKER_IMAGE_BACKEND = 'node-backend'
        GITHUB_CREDENTIALS_ID = 'github-credentials' // Use the ID you created
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    echo "Checking out from GitHub repository"
                }
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']], // Change 'main' to your branch name if different
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    userRemoteConfigs: [[
                        url: 'https://github.com/MedAdemBHA/React-Inventory-Management-System-main.git', 
                        credentialsId: "${GITHUB_CREDENTIALS_ID}"
                    ]]
                ])
            }
        }
        stage('Build Frontend') {
            steps {
                dir('Frontend') {
                    script {
                        try {
                            sh 'docker build -t $DOCKER_IMAGE_FRONTEND .'
                        } catch (Exception e) {
                            error "Failed to build frontend: ${e.message}"
                        }
                    }
                }
            }
        }
        stage('Build Backend') {
            steps {
                dir('Backend') {
                    script {
                        try {
                            sh 'docker build -t $DOCKER_IMAGE_BACKEND .'
                        } catch (Exception e) {
                            error "Failed to build backend: ${e.message}"
                        }
                    }
                }
            }
        }
        stage('Test Frontend') {
            steps {
                dir('Frontend') {
                    script {
                        try {
                            sh 'docker run --rm $DOCKER_IMAGE_FRONTEND npm test'
                        } catch (Exception e) {
                            error "Failed to test frontend: ${e.message}"
                        }
                    }
                }
            }
        }
        stage('Test Backend') {
            steps {
                dir('Backend') {
                    script {
                        try {
                            sh 'docker run --rm $DOCKER_IMAGE_BACKEND npm test'
                        } catch (Exception e) {
                            error "Failed to test backend: ${e.message}"
                        }
                    }
                }
            }
        }
        stage('Push Frontend') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        try {
                            sh 'docker login -u $DOCKER_USER -p $DOCKER_PASS'
                            sh 'docker tag $DOCKER_IMAGE_FRONTEND $DOCKER_USER/$DOCKER_IMAGE_FRONTEND:latest'
                            sh 'docker push $DOCKER_USER/$DOCKER_IMAGE_FRONTEND:latest'
                        } catch (Exception e) {
                            error "Failed to push frontend: ${e.message}"
                        }
                    }
                }
            }
        }
        stage('Push Backend') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        try {
                            sh 'docker login -u $DOCKER_USER -p $DOCKER_PASS'
                            sh 'docker tag $DOCKER_IMAGE_BACKEND $DOCKER_USER/$DOCKER_IMAGE_BACKEND:latest'
                            sh 'docker push $DOCKER_USER/$DOCKER_IMAGE_BACKEND:latest'
                        } catch (Exception e) {
                            error "Failed to push backend: ${e.message}"
                        }
                    }
                }
            }
        }
    }
}
