pipeline {
    agent { docker { image 'luk020/stocks-devops:latest' } }
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