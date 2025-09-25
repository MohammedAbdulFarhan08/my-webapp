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
        //just a change
        stage('Build Docker Image') {
            steps {
                script {
                    
                    env.IMAGE_TAG = "${IMAGE_NAME}:${BUILD_NUMBER}"
                    sh "docker build -t ${IMAGE_TAG} ."
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
                sh 'docker push ${IMAGE_TAG}'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                  # Remove old container if exists
                  docker rm -f my-node-app || true
                  
                  # Run container with restart policy
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
