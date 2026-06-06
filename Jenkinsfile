pipeline {

    agent any

    environment {
        IMAGE_NAME = "sarvnoorkaur/my-web-app"
        IMAGE_TAG = "latest"
        KUBECONFIG = 'C:/Users/sarvn/.kube/config'
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
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh '''
                        echo "$PASS" | docker login -u "$USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }

        stage('Check Kubernetes') {
            steps {
                sh 'kubectl config current-context'
                sh 'kubectl get nodes'
            }
        }

        stage('Deploy To Kubernetes') {
            steps {
                sh """
                kubectl set image deployment/web-deployment \
                web-container=${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }
    }
}