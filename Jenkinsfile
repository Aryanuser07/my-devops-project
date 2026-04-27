pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/yourusername/my-devops-project.git'
        GIT_BRANCH = 'main'
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'
        DOCKER_IMAGE = 'yourdockerhubusername/myapp'
        CONTAINER_NAME = 'myapp-container'
        APP_PORT = '8085'
        CONTAINER_PORT = '80'
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: "${GIT_BRANCH}", url: "${GIT_REPO}"
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} ."
                bat "docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest"
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                withDockerRegistry([credentialsId: "${DOCKERHUB_CREDENTIALS}", url: '']) {
                    bat "docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}"
                    bat "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }

        stage('Remove Old Container') {
            steps {
                bat "docker stop ${CONTAINER_NAME} || exit 0"
                bat "docker rm ${CONTAINER_NAME} || exit 0"
            }
        }

        stage('Run New Container') {
            steps {
                bat "docker run -d --name ${CONTAINER_NAME} -p ${APP_PORT}:${CONTAINER_PORT} ${DOCKER_IMAGE}:${BUILD_NUMBER}"
            }
        }
    }
}