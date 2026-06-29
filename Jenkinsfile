pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "appu9880/flaskmarket"
        DOCKER_TAG = "latest"
        DOCKER_CREDENTIALS = "dockerhub-creds"
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

        stage('4. Run Ansible Playbook') {
            steps {
                echo 'Deploying to Kubernetes via Ansible...'
                sh "ansible-playbook /home/jagan/ansible/deploy.yml"
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