pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/vineeth394/weather-app.git'
        BRANCH = 'feature-1'
        IMAGE_NAME = 'weather-app'
        DOCKER_COMPOSE_FILE = 'docker-compose.yml'
        EC2_USER = 'Docker-instance'  // Update based on your AMI
        EC2_HOST ='access-key'  
        EC2_HOST1 = 'access-password'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: "${BRANCH}", url: "${REPO_URL}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:latest ."
                }
            }
        }


        stage('Deploy on EC2') {
            steps {
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: 'EC2_SSH_KEY', keyFileVariable: 'SSH_KEY')]) {
                        sh """
                        ssh -i ${SSH_KEY} ${EC2_USER}@${EC2_HOST} << 'EOF'
                        sudo docker load -i /home/${EC2_USER}/${IMAGE_NAME}.tar
                        cd /home/${EC2_USER}/
                        sudo docker-compose down
                        sudo docker-compose up -d
                        EOF
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
