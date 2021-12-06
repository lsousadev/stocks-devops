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
                withCredentials([
                    [$class: 'VaultTokenCredentialBinding', credentialsId: 'vault-root', vaultAddr: 'http://192.168.1.165:8200']
                ]){
                    sh '''
                        export role_id=`curl --header "X-Vault-Token: $VAULT_TOKEN" $VAULT_ADDR/v1/auth/approle/role/jenkins/role-id | jq -r ".data.role_id"`
                        export secret_id=`curl --header "X-Vault-Token: $VAULT_TOKEN" --request POST $VAULT_ADDR/v1/auth/approle/role/jenkins/secret-id | jq -r ".data.secret_id"`
                        results=`curl --header "X-Vault-Request: true" --header "X-Vault-Token: $VAULT_TOKEN" $VAULT_ADDR/v1/secret/data/docker`
                        echo $results

                        docker build -t luk020/stocks-devops:latest -f ./app-image-build/Dockerfile .
                        docker push luk020/stocks-devops:latest
                    '''
                }
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