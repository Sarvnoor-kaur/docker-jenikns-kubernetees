pipeline {

    agent any

    environment {
        IMAGE_NAME = "sarvnoorkaur/my-web-app"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Checkout Source') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Sarvnoor-kaur/docker-jenikns-kubernetees.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE_NAME%:%IMAGE_TAG% ."
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {

                    bat "docker login -u %USER% -p %PASS%"
                }
            }
        }

        stage('Push Image') {
            steps {
                bat "docker push %IMAGE_NAME%:%IMAGE_TAG%"
            }
        }

        stage('Deploy To Kubernetes') {
            steps {
                bat """
                kubectl set image deployment/web-deployment ^
                web-container=%IMAGE_NAME%:%IMAGE_TAG%
                """
            }
        }
    }
}