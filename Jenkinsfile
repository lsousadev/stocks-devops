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
                // the dot in docker build sets the "context" (pwd) of the builder environment to . for ref inside Dockerfile
                // context explanation: https://stackoverflow.com/questions/27068596/how-to-include-files-outside-of-dockers-build-context
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