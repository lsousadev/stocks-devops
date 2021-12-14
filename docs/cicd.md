## CI/CD Implementation Steps

### Jenkins Server Container

- run containerized jenkins according to dockerhub image instructions
    - https://github.com/jenkinsci/docker/blob/master/README.md
    - `docker run -d -v jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts-jdk11`

### Jenkins Build Agent/Worker Container

https://devopscube.com/docker-containers-as-build-slaves-jenkins/

- Run Jenkins Server Container w/ Docker Binded to Host Machine
    ```
    docker run -d -p 8080:8080 -p 50000:50000 \
    -v jenkins_home:/var/jenkins_home \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v $(which docker):/usr/bin/docker \
    jenkins/jenkins:lts-jdk11
    ```
- Configure a Docker Host With Remote API
    - install docker in host machine
    - change permissions of docker.sock according to needs, eg. - `chmod 777 /run/docker.sock`
    - change `ExecStart` line of host machine > /lib/systemd/system/docker.service to `ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:4243 -H unix:///var/run/docker.sock`
    - reload services: `sudo systemctl daemon-reload && sudo service docker restart`
    - test: `curl <host_machine_ip>:4243/version`
- Create a Jenkins Agent Docker Image
    - copy image from link above and tweaked a bit for project's needs, see stocks-devops/app-image-build/Dockerfile
        - installs ssh server to receive ssh connections from jenkins server container through host machine docker engine
        - creates user jenkins with password to be used by jenkins server on ssh connection
        - installs dependencies (docker to build and push the app image)
    - build image in host machine
- Configure Jenkins Server With Docker Plugin
    - install docker-plugin
    - find cloud configuration page
    - fill out fields according to link above
        - docker URI: tcp://<host_machine_ip>:4243
        - test connection
        - label and name of choice, eg. agent-with-docker
        - docker image: agent image name built in previous step
        - remote filing system root: home directory of user created in Dockerfile (jenkins)\
        - credentials: add and use SSH username and password created in previous step
- Make sure Jenkinsfile is using `agent { docker { image 'luk020/jenkins-worker' } }`

### SCM Setup

- create repo on a Git repo remote host (`stocks-devops` on my github account)
- initialize Git on local app directory with `git init`
- link local Git with remote repo using `git remote add origin <url>`

### Jenkins Multibranch Pipeline

- create multibranch pipeline with github push/PR as trigger
- install docker pipeline plugin (not installed with "recommended" plugins)
- use ngrok to create a public IP for jenkins container's host so github can trigger builds via push notifications:
    1. download ngrok https://ngrok.com/download on server host
    2. create ngrok free account in website
    3. add ngrok token with `ngrok authtoken <token>` or as config file in ~/.ngrok2/ngrok.yml
    4. start a tunnel that delivers requests to host on port 8080: `nohup ngrok http 8080 --log=stdout > ngrok.log &`
    5. copy-paste ngrok url to Manage Jenkins > Configure System > Jenkins Location > Jenkins URL
        - `curl http://127.0.0.1:4040/api/tunnels --silent | jq '.tunnels | .[] | .public_url'`
        - Jenkins should automatically change the webhook url in GH repo to <ngrok url>/github-webhook/ (double-check)
- steps to make github and jenkins work together:
    1. create a personal access token (PAT) on GH with scopes: repo:*, admin:repo_hook, admin:org_hook
    2. create secret text credentials on Jenkins with PAT as secret (for Jenkins to create webhooks in GH repo)
    3. create username & password credentials on Jenkins with GH username & PAT (for job to pull repo + branches)
    4. Manage Jenkins > Configure System > GitHub > Add GitHub Server: add credentials from step 2, check "Manage Hooks"
    5. create multibranch pipeline using credentials from step 3 and change Discover Branches setting to "All Branches"

### Vault Setup

- pull vault server docker image with `docker pull vault`
- `mkdir -p /vault/config`
- `vim /vault/config/local.json`
    ```
    {
        "listener": [{
            "tcp": {
                "address": "<ip>:8200",
                "tls_disable" : 1
            }
        }],
        "storage" :{
            "file" : {
                "path" : "/vault/data"
            }
        },
        "max_lease_ttl": "10h",
        "default_lease_ttl": "10h",
        "ui": true
    }
    ```
- `docker run -d -v /vault:/vault -p 8200:8200 --cap-add=IPC_LOCK vault server`
- host machine's /vault owner needs to be changed to container's vault user (in my case user ID 100):
    - `sudo chown -vR 100 /vault`
- `vault operator init`:
    ```
    Unseal Key 1: gr8mjp6mo7FiduDZ2YGHgbiCAh0IiN5QZK5uSpj84FQo
    Unseal Key 2: /Y5656KvAfMh78sR3qsyCK+H5Mc05eaBdLuw7RxzcJ2C
    Unseal Key 3: 5dAPzRONuc21mtXAywh/nOmV0RlY00S3ywco8KXJcmO7
    Unseal Key 4: wNLKeer3v+OYTIB2vnQ4G0nLBdk88Q6x+AWTxmsHFmyk
    Unseal Key 5: DOlBAhAiDOG0d7iPwnI2ZrIpmME2EIK669tu+2MwayE/
    Initial Root Token: s.1TLYOZ5gv5KnJjIXx7IBlRKO
    ``` 
- `vault operator unseal <unseal-key>` (x3)
- `vault login <root-token>`
- `vault secrets enable -path=secret kv`
- `vault kv put /secret/docker username="<username>" password="<password>"`
- enable approle auth method in server with `vault auth enable approle`
- `vim /vault/policies/approle-stocks-devops`:
    ```
    path "secret/docker" {
        capabilities = [ "read" ]
    }
    ```
- `vault policy write approle-stocks-devops /vault/policies/approle-stocks-devops`
- create approle role:
    ```
    vault write auth/approle/role/stocks-devops token_policies="approle-stocks-devops" \
        secret_id_ttl=10m \
        token_num_uses=2 \
        token_ttl=20m \
        token_max_ttl=30m \
        secret_id_num_uses=4
    ```
- install Vault plugin in Jenkins server
    - configure plugin with server address (http://<ip>:8200) and credentials (root token)

### Jenkinsfile

- check Jenkinsfile in source code (useful comments in code)