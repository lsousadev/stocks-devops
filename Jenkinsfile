pipeline {
    agent { docker { image 'luk020/jenkins-worker' } }
    //agent any
    stages {
        stage('prepare') {
            steps {
                sh '''
                    uname -a
                    pwd
                    ls -lah
                    whoami
                '''
            }
        }
        stage('build image') {
            steps {
                sh '''
                    docker build -t luk020/stocks-devops:latest -f ./app-image-build/Dockerfile .
                    docker push luk020/stocks-devops:latest
                '''
            }
        }
    }
    post {
        always {
            sh '''
                uname -a
                pwd
                ls -lah
                whoami
            '''
        }
    }
}