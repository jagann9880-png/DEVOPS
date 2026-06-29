
pipeline {
    agent any
 
    environment {
        DOCKER_IMAGE = "appu9880/flaskmarket"
        DOCKER_TAG = "latest"
        DOCKER_CREDENTIALS = "dockerhub-creds"
        K8S_MASTER_IP = "16.112.206.30"
        SSH_KEY = "/var/lib/jenkins/.ssh/k8s_key"
    }
 
    stages {
 
        stage('1. Clone Code from GitHub') {
            steps {
                echo 'Pulling latest code from GitHub...'
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/jagann9880-png/DEVOPS.git'
            }
        }
 
        stage('2. Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }
 
        stage('3. Push Image to Docker Hub') {
            steps {
                echo 'Pushing image to Docker Hub...'
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_CREDENTIALS}",
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }
 
        stage('4. Deploy to Kubernetes') {
            steps {
                echo 'Deploying to Kubernetes via kubectl over SSH...'
                sh """
                    ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no ubuntu@${K8S_MASTER_IP} '
                        kubectl apply -f /home/ubuntu/k8s/deployment.yml &&
                        kubectl apply -f /home/ubuntu/k8s/service.yml &&
                        kubectl rollout restart deployment/flaskmarket-deployment &&
                        kubectl get pods
                    '
                """
            }
        }
 
    }
 
    post {
        success {
            echo '✅ Pipeline completed! Flask app deployed to Kubernetes.'
        }
        failure {
            echo '❌ Pipeline failed. Check the logs above.'
        }
    }
}
 
