pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'sai127001'
        DOCKER_HUB_REPO = 'mock-repo'
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-creds') // Jenkins stored credentials
        IMAGE_NAME = 'sai127001/nginx-app'  // Update with your Docker Hub repo
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                git branch: 'master', credentialsId: 'git', url: 'https://github.com/SAI127001/Static-Web-Docker.git' 
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${env.DOCKER_HUB_USER}/${env.IMAGE_NAME}:${env.IMAGE_TAG} ."
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_HUB_USER', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                    sh '''
                        echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USER --password-stdin
                    '''
                }
                sh "docker push ${env.DOCKER_HUB_USER}/${env.DOCKER_HUB_REPO}:${env.IMAGE_TAG}"
            }
        }

        stage('Run Nginx Container in Background') {
            steps {
                script {
                    sh "docker run -d --name nginx-server -p 80:80 ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }

    post {
        success {
            echo "Nginx container is running on port 80!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
