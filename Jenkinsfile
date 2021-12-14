pipeline {
    agent { docker { image 'luk020/jenkins-worker' } }
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
                // Code syntax help: Project > Pipeline Syntax > Snippet Generator > withCredentials 
                withCredentials([
                    [$class: 'VaultTokenCredentialBinding', credentialsId: 'vault-root', vaultAddr: 'http://192.168.1.11:8200']
                ]){
                // 1) "set -" unsets the -v and -x shell attributes, which print shell input lines as they are read & print trace of simple commands,
                // respectively. Used below to hide secrets used in CLI. More info: https://gnu.org/software/bash/manual/html_node/The-Set_Builtin.html
                // 2) The dot in the "docker build" line sets the "context" (pwd) of the builder environment to . for ref inside Dockerfile.
                // More info on docker "context": https://stackoverflow.com/questions/27068596/how-to-include-files-outside-of-dockers-build-context
                    sh '''
                        # set -
                        
                        export role_id=`curl --header "X-Vault-Token: $VAULT_TOKEN" $VAULT_ADDR/v1/auth/approle/role/jenkins/role-id | jq -r ".data.role_id"`
                        export secret_id=`curl --header "X-Vault-Token: $VAULT_TOKEN" --request POST $VAULT_ADDR/v1/auth/approle/role/jenkins/secret-id | jq -r ".data.secret_id"`
                        results_docker=`curl --header "X-Vault-Request: true" --header "X-Vault-Token: $VAULT_TOKEN" $VAULT_ADDR/v1/secret/data/docker`
                        
                        for i in $(echo $results_docker | jq -r '.data.data|keys[]')
                        do
                            export $i=$(echo $results_docker | jq -r .data.data.$i)
                        done

                        docker login -u $username -p $password
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