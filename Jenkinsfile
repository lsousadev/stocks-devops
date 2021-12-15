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
                    [$class: 'VaultUsernamePasswordCredentialBinding', credentialsId: 'vault-docker', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME']
                ]){
                // 1) "set -" unsets the -v and -x shell attributes, which print shell input lines as they are read & print trace of simple commands,
                // respectively. Used below to hide secrets used in CLI. More info: https://gnu.org/software/bash/manual/html_node/The-Set_Builtin.html
                // 2) The dot in the "docker build" line sets the "context" (pwd) of the builder environment to . for ref inside Dockerfile.
                // More info on docker "context": https://stackoverflow.com/questions/27068596/how-to-include-files-outside-of-dockers-build-context
                    sh '''
                        set -
                        
                        # The block below is no longer needed as docker credential retrieval is now handled by the Vault plugin and its Credentials integration

                        # export role_id=`curl --header "X-Vault-Token: $VAULT_TOKEN" $VAULT_ADDR/v1/auth/approle/role/stocks-devops/role-id | jq -r ".data.role_id"`
                        # export secret_id=`curl --header "X-Vault-Token: $VAULT_TOKEN" --request POST $VAULT_ADDR/v1/auth/approle/role/stocks-devops/secret-id | jq -r ".data.secret_id"`
                        # export token=`curl --request POST --data '{"role_id":"'"$role_id"'","secret_id":"'"$secret_id"'"}' $VAULT_ADDR/v1/auth/approle/login | jq -r ".auth.client_token"`
                        # results_docker=`curl --header "X-Vault-Request: true" --header "X-Vault-Token: $token" $VAULT_ADDR/v1/secret/data/docker`
                        # for i in $(echo $results_docker | jq -r '.data.data|keys[]')
                        # do
                        #     export $i=$(echo $results_docker | jq -r .data.data.$i)
                        # done

                        docker login -u $USERNAME -p $PASSWORD
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