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
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    bat """
                        echo %PASS% | docker login -u %USER% --password-stdin
                    """
                }
            }
        }

        stage('Push Image') {
            steps {
                bat "docker push %IMAGE_NAME%:%IMAGE_TAG%"
            }
        }

        stage('Check Kubernetes') {
            steps {
                bat "kubectl get nodes"
            }
        }

        stage('Deploy To Kubernetes') {
            steps {
                bat """
                    kubectl set image deployment/web-deployment ^
                    web-container=%IMAGE_NAME%:%IMAGE_TAG%

                    kubectl rollout status deployment/web-deployment
                """
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs."
        }
    }
}
