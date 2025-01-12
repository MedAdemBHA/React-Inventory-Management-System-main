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
            }
        }
        stage('Build Frontend') {
            steps {
                dir('Frontend') {
                    script {
                        try {
                            bat 'docker build -t %DOCKER_IMAGE_FRONTEND% .'
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
                            bat 'docker build -t %DOCKER_IMAGE_BACKEND% .'
                        } catch (Exception e) {
                            error "Failed to build backend: ${e.message}"
                        }
                    }
                }
            }
        }
        stage('Scan Frontend') {
            steps {
                script {
                    try {
                        bat 'trivy image %DOCKER_IMAGE_FRONTEND%'
                    } catch (Exception e) {
                        error "Failed to scan frontend: ${e.message}"
                    }
                }
            }
        }
        stage('Scan Backend') {
            steps {
                script {
                    try {
                        bat 'trivy image %DOCKER_IMAGE_BACKEND%'
                    } catch (Exception e) {
                        error "Failed to scan backend: ${e.message}"
                    }
                }
            }
        }
        stage('Push Frontend') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        try {
                            bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                            bat 'docker tag %DOCKER_IMAGE_FRONTEND% %DOCKER_USER%/%DOCKER_IMAGE_FRONTEND%:latest'
                            bat 'docker push %DOCKER_USER%/%DOCKER_IMAGE_FRONTEND%:latest'
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
                            bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                            bat 'docker tag %DOCKER_IMAGE_BACKEND% %DOCKER_USER%/%DOCKER_IMAGE_BACKEND%:latest'
                            bat 'docker push %DOCKER_USER%/%DOCKER_IMAGE_BACKEND%:latest'
                        } catch (Exception e) {
                            error "Failed to push backend: ${e.message}"
                        }
                    }
                }
            }
        }
    }
}
