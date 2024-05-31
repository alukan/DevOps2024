
pipeline {
    agent any

    environment {
        PATH = "/usr/local/go/bin:${PATH}"
    }


    stages {
        stage('Build') {
            steps {
                sh 'go build main.go'
            }
        }
        stage('Deploy') {
            environment {
                ANSIBLE_HOST_KEY_CHECKING = 'false'
            }
            steps {
                ansiblePlaybook credentialsId: 'mykey',
                                inventory: 'hosts.ini',
                                playbook: 'playbook.yml'
            }
        }
    }
}

