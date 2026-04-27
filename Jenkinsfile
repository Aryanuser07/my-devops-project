pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'aryanuser07/myapp'
        CONTAINER_NAME = 'myapp-container'
        APP_PORT = '8085'
        CONTAINER_PORT = '80'
    }

    stages {

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %DOCKER_IMAGE%:%BUILD_NUMBER% ."
                bat "docker tag %DOCKER_IMAGE%:%BUILD_NUMBER% %DOCKER_IMAGE%:latest"
            }
        }

        stage('DockerHub Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat "docker login -u %DOCKER_USER% -p %DOCKER_PASS%"
                }
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                bat "docker push %DOCKER_IMAGE%:%BUILD_NUMBER%"
                bat "docker push %DOCKER_IMAGE%:latest"
            }
        }

        stage('Remove Old Container') {
            steps {
                bat "docker stop %CONTAINER_NAME% || exit 0"
                bat "docker rm %CONTAINER_NAME% || exit 0"
            }
        }

        stage('Run New Container') {
            steps {
                bat "docker run -d --name %CONTAINER_NAME% -p %APP_PORT%:%CONTAINER_PORT% %DOCKER_IMAGE%:%BUILD_NUMBER%"
            }
        }
    }
}