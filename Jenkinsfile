pipeline {
    //agent { docker { image 'python:3.5.1' } }
    agent any
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
                    cd ./app-image-build
                    docker build -t luk020/stocks-devops:latest .
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