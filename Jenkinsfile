pipeline {
    agent any

    environment {
        PATH = "/usr/local/go/bin:${env.PATH}"
        REPO_DIR = 'DevOps2024'
    }

    tools {
        go 'go1.22.3'
    }

    stages {
        stage('Pull Repository') {
            steps {
                script {
                    sh """
                        cd ${REPO_DIR}
                        git pull
                    """
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    sh 'go version'
                    sh """
                        cd ${REPO_DIR}
                        go build main.go
                    """
                }
            }
        }
        stage('Deploy') {
            environment {
                ANSIBLE_HOST_KEY_CHECKING = 'false'
            }
            steps {
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

