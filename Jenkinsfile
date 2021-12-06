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
                    export VAULT_ADDR='http://192.168.1.165:8200'
                    export role_id=`vault read --field="role_id" auth/approle/role/stocks-devops/role-id`
                    export secret_id=`vault write --field="secret_id" -f auth/approle/role/stocks-devops/secret-id`
                    export VAULT_TOKEN=`vault write auth/approle/login role_id=$role_id secret_id=$secret_id`
                    results=`vault kv get secret/docker`
                    echo $results

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