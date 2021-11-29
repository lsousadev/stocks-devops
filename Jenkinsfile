pipeline {
    //agent { docker { image 'python:3.5.1' } }
    agent { any }
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