pipeline {
    agent any
    environment {
        GIT_REPOSITORY_URL = 'https://github.com/blackopsGun/jenkins-python.git'
        DOCKER_IMAGE_NAME = 'blackopsgun/jenkins-python'
        IMAGE_TAG = '1.0'
    }
    stages {
        stage('Clone Repository') {
            steps {
                script {
                    try {
                        git branch: 'main', url: GIT_REPOSITORY_URL
                    } catch (Exception e) {
                        echo "Failed to clone repository: ${e.message}"
                        error "Pipeline aborted: Failed to clone repository."
                    }
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    try {
                        docker.build("${DOCKER_IMAGE_NAME}:${IMAGE_TAG}")
                    } catch (Exception e) {
                        echo "Failed to build Docker image: ${e.message}"
                        error "Pipeline aborted: Failed to build Docker image."
                    }
                }
            }
        }
        stage('Push to DockerHub') {
            steps {
                script {
                    try {
                        withCredentials([usernamePassword(credentialsId: 'my-docker-hub-credential-id', 
                                                          usernameVariable: 'DOCKER_USERNAME', 
                                                          passwordVariable: 'DOCKER_PASSWORD')]) {
                            sh """
                            echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                            docker push ${DOCKER_IMAGE_NAME}:${IMAGE_TAG}
                            docker logout
                            """
                        }
                    } catch (Exception e) {
                        echo "Failed to push Docker image to registry: ${e.message}"
                        error "Pipeline aborted: Failed to push Docker image."
                    }
                }
            }
        }
    }
}

