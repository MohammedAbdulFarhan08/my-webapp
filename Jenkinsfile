pipeline {
    agent any

    tools {
        nodejs "nodejs-18"
    }

    environment {
        REGISTRY = "localhost:5000"   
        IMAGE_NAME = "my-node-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/MohammedAbdulFarhan08/my-webapp.git'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    def GIT_COMMIT = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()

                    env.IMAGE_TAG = "${REGISTRY}/${IMAGE_NAME}:${GIT_COMMIT}"
                    env.LATEST_TAG = "${REGISTRY}/${IMAGE_NAME}:latest"

                    sh """
                      docker build -t ${IMAGE_TAG} -t ${LATEST_TAG} .
                    """
                }
            }
        }

        stage('Push Image') {
            steps {
                sh """
                  docker push ${IMAGE_TAG}
                  docker push ${LATEST_TAG}
                """
            }
        }
    }
}
