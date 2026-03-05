pipeline {
    agent any

    environment {
        IMAGE_NAME = "yourdockerhubusername/mynodeapp"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Run Container (Test)') {
            steps {
                script {
                    sh "docker run -d -p 3000:3000 --name test-container ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "sleep 5"
                    sh "curl -f http://localhost:3000 || exit 1"
                    sh "docker stop test-container && docker rm test-container"
                }
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                        sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                        sh "docker logout"
                    }
                }
            }
        }

        stage('Optional: Docker Compose Up') {
            steps {
                script {
                    sh "docker-compose up -d"
                }
            }
        }
    }
}
