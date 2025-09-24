pipeline {
    agent any

    tools {
        nodejs "nodejs-18"
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
                    dockerImage = docker.build("my-node-app:latest")
                }
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                  docker rm -f my-node-app || true
                  docker run -d --name my-node-app -p 3000:3000 my-node-app:latest
                '''
            }
        }
    }
}
