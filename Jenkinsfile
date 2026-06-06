pipeline {

    agent any

    environment {
        IMAGE_NAME = "sarvnoorkaur/my-web-app"
        IMAGE_TAG = "latest"

        // IMPORTANT: must exist inside Jenkins container
        KUBECONFIG = "/root/.kube/config"
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
                sh '''
                    echo "Checking Kubernetes connection..."
                    kubectl --kubeconfig=$KUBECONFIG get nodes
                '''
            }
        }

        stage('Deploy To Kubernetes') {
            steps {
                sh '''
                    echo "Updating Kubernetes deployment image..."

                    kubectl --kubeconfig=$KUBECONFIG set image deployment/web-deployment \
                    web-container=${IMAGE_NAME}:${IMAGE_TAG}

                    kubectl --kubeconfig=$KUBECONFIG rollout status deployment/web-deployment
                '''
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