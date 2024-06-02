pipeline {
    agent any

    environment {
        PATH = "/usr/local/go/bin:${env.PATH}"
        REPO_DIR = 'DevOps2024'
    }

    stages {
        stage('Pull Repository') {
            steps {
                script {
                    sh """
                        cd ${REPO_DIR}
                        git pull origin docker
                    """
                }
            }
        }
        stage('Build image') {
            steps {
                script {
                    sh """
                        cd ${REPO_DIR}
                        docker build -t ttl.sh/alukan-devops .
                    """
                    sh 'docker images'
                }
            }
        }
        stage('Push image') {
            steps {
                script {
                    sh 'docker push ttl.sh/alukan-devops'
                }
            }
        }
        stage('Deploy') {
            environment {
                ANSIBLE_HOST_KEY_CHECKING = 'false'
            }
            steps {
                script {
                    sh """
                        cd ${REPO_DIR}
                        ls -la
                    """
                    ansiblePlaybook credentialsId: 'my-ssh-key',
                                    inventory: "${REPO_DIR}/hosts.ini",
                                    playbook: "${REPO_DIR}/playbook.yml"
                }
            }
        }
    }
}

