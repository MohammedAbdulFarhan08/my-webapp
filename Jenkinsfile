pipeline {
    agent any

    tools {
        nodejs "nodejs-18"
    }

    environment {
        IMAGE_NAME = "mohammedabdulfarhan/my-node-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/MohammedAbdulFarhan08/my-webapp.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def GIT_COMMIT = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()

                    env.IMAGE_TAG = "${IMAGE_NAME}:${GIT_COMMIT}"
                    env.LATEST_TAG = "${IMAGE_NAME}:latest"

                    sh """
                      docker build -t ${IMAGE_TAG} -t ${LATEST_TAG} .
                    """
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'farhan-dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh """
                  docker push ${IMAGE_TAG}
                  docker push ${LATEST_TAG}
                """
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                  # Remove old container if exists
                  docker rm -f my-node-app || true
                  
                  # Run container with commit hash image
                  docker run -d --name my-node-app -p 3000:3000 --restart unless-stopped ${IMAGE_TAG}
                '''
            }
        }
    }

    post {
        failure {
            echo "Pipeline failed! Cleaning up..."
            sh 'docker rm -f my-node-app || true'
        }
        success {
            echo "Pipeline completed successfully!"
        }
    }
}
