pipeline {
    agent any

    environment {
        PATH = "/usr/local/go/bin:${env.PATH}"
        REPO_DIR = 'DevOps2024'
        DOCKER_IMAGE = 'ttl.sh/alukan-devops:latest'
        CONTAINER_NAME = 'alukan-devops'
        CONTAINER_PORT = '4444'
    }

    stages {
        stage('Pull Repository') {
            steps {
                script {
                    sh """
                        #git clone https://github.com/alukan/DevOps2024.git
                        cd ${REPO_DIR}
                        git checkout aws
                        git pull origin aws
                    """
                }
            }
        }
        stage('Build image') {
            steps {
                script {
                    sh """
                        cd ${REPO_DIR}
                        docker build -t ${DOCKER_IMAGE} .
                    """
                    sh 'docker images'
                }
            }
        }
        stage('Push image') {
            steps {
                script {
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }
        stage('Deploy stage') {
            steps {
                script {
                    sh """
                        ssh -i /var/lib/jenkins/.ssh/id_rsa vagrant@192.168.105.3 <<EOF
                        sudo systemctl start docker
                        # sudo apt update
                        # sudo apt install -y docker.io
                        echo "Listing all running containers:"
                        sudo docker ps -a
                        sudo docker stop ${CONTAINER_NAME} || true
                        sudo docker rm ${CONTAINER_NAME} || true
                        sudo docker pull ${DOCKER_IMAGE}
                        
                        # Run the new Docker container
                        sudo docker run -d --name ${CONTAINER_NAME} -p ${CONTAINER_PORT}:${CONTAINER_PORT} ${DOCKER_IMAGE}
                    """
                }
            }
        }
        stage('Deploy production') {
            steps {
                script {
                    sh """
                        ssh -o StrictHostKeyChecking=no -i /jenkins.pem ubuntu@ec2-15-237-195-221.eu-west-3.compute.amazonaws.com <<EOF
                        # sudo apt update
                        # sudo apt install -y docker.io
                        sudo systemctl start docker
                        echo "Listing all running containers:"
                        sudo docker ps -a
                        sudo docker stop ${CONTAINER_NAME} || true
                        sudo docker rm ${CONTAINER_NAME} || true
                        sudo docker pull ${DOCKER_IMAGE}
                        
                        # Run the new Docker container
                        sudo docker run -d --name ${CONTAINER_NAME} -p ${CONTAINER_PORT}:${CONTAINER_PORT} ${DOCKER_IMAGE}
                    """
                }
            }
        }
    }
}

