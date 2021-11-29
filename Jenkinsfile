pipeline {
    //agent { docker { image 'python:3.5.1' } }
    agent { docker { image 'ubuntu:latest' } }
    stages {
        stage('build') {
            steps {
                sh '''
                    uname -a
                    pwd
                    ls -lah
                '''
            }
        }
    }
}