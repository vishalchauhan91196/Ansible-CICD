pipeline {
    agent any

    stages {
        stage('GIT CHECKOUT') {
            steps {
                git branch: 'main', url: 'https://github.com/vishalchauhan91196/Ansible-CICD.git'
            }
        }

        stage('SENDING DOCKER FILE FROM JENKINS TO ANSIBLE SERVER OVER SSH') {
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
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.36.214 cd /home/ubuntu/'
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.36.214 docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                }
            }
        }

        stage('DOCKER TAG IMAGE') {
            steps {
                sshagent(['ansible_creds']) {
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.36.214 cd /home/ubuntu/'
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.36.214 docker image tag $JOB_NAME:v1.$BUILD_ID vishalchauhan9/$JOB_NAME:v1.$BUILD_ID'
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.36.214 docker image tag $JOB_NAME:v1.$BUILD_ID vishalchauhan9/$JOB_NAME:latest'
                }
            }
        }

        stage('PUSH DOCKER IMAGE') {
            steps {
                sshagent(['ansible_creds']) {
                    withCredentials([string(credentialsId: 'dockerhub_password', variable: 'dockerhub_password')]) {
                        sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.36.214 docker login -u vishalchauhan9 -p ${dockerhub_password}"
                        sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.36.214 docker image push vishalchauhan9/$JOB_NAME:v1.$BUILD_ID'
                        sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.36.214 docker image push vishalchauhan9/$JOB_NAME:latest'
                        sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.36.214 docker rmi -f $(docker images -aq)'
                    }
                }
            }
        }

        stage('SENDING DEPLOYMENT FILEs FROM JENKINS TO KUBERNETES SERVER OVER SSH') {
            steps {
                sshagent(['ansible_creds']) {
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.63.91'
                    sh 'scp /var/lib/jenkins/workspace/ansiblecicd/* ubuntu@172.31.63.91:/home/ubuntu/'
                }
            }
        }

    }
}
