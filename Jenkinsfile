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
				script{
					try{
						git branch: 'main', url: GIT_REPOSITORY_URL
					} catch (Exception e) {
						echo "Failed to clone repository: ${e.message}"
						error "Failed to clone repo"
						}
				}
                        }
                }
                stage('Build Docker Image') {
                        steps {
				script {
					try{
						docker.build("${DOCKER_IMAGE_NAME}:${IMAGE_TAG}")
					} catch (Exception e){
						echo "Failed to build docker image: ${e.message}"
						error "Failed to build docker image"
						}
				}
                        }
                }
                stage('Push to DockerHub') {
                        steps {
			script {
				try{
					withCredentials([usernamePassword(credentialsId: 'my-docker-hub-credential-id', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
								// explicit login before push
								sh """
								echo "$DOCKER_PASSWORD" | docker login -u "DOCKER_USERNAME" --password-stdin
								docker push ${DOCKER_IMAGE_NAME}:${IMAGE_TAG}
								"""
				}
					} catch (Exception e){
						echo "Failed to push docker image to registry: ${e.message}"
						error "Failed to push docker image"
						}
				}
                        }
                }
        }
}

