pipeline {
    agent any

    tools {
        nodejs "nodejs-18"
    }

    environment {
        REGISTRY = "51.21.129.79:5000"    
        IMAGE_NAME = "my-node-app"
        CREDENTIAL_ID = "details"       
        CONTAINER_NAME = "my-node-app"
        APP_PORT = "3000"
    }
 //
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

                    sh "docker build -t ${IMAGE_TAG} ."
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry("http://${REGISTRY}", "${CREDENTIAL_ID}") {
                        sh "docker push ${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    
                    sh "docker rm -f ${CONTAINER_NAME} || true"

                    
                    sh """
                        docker run -d --name ${CONTAINER_NAME} \
                        -p ${APP_PORT}:${APP_PORT} \
                        --restart unless-stopped \
                        ${IMAGE_TAG}
                    """
                }
            }
        }
    }

    post {
        failure {
            echo "Pipeline failed! Cleaning up..."
            sh "docker rm -f ${CONTAINER_NAME} || true"
        }
        success {
            echo "Pipeline completed successfully!"
        }
    }
}
