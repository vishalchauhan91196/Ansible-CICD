pipeline {
    agent any

    stages {
        stage('GIT CHECKOUT') {
            steps {
                git branch: 'main', url: 'https://github.com/vishalchauhan91196/Ansible-CICD.git'
            }
        }

        stage('SENDING DOCKER FILE FROM JENKINS TO ANSIBLE OVER SSH') {
            steps {
                sshagent(['ansible_creds']) {
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.36.214'
                    sh 'scp /var/lib/jenkins/workspace/ansiblecicd/Dockerfile ubuntu@172.31.36.214:/home/ubuntu/'
                }
            }
        }

        stage('DOCKER BUILD IMAGE') {
            steps {
                sshagent(['ansible_creds']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@172.31.36.214
                    cd /home/ubuntu/
                    docker image build -t $JOB_NAME:v1.$BUILD_ID .
                    '''
                }
            }
        }
    }
}
