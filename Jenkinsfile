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
                    echo "Nothing to see here. Not yet."
                '''
            }
        }
    }
}