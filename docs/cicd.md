## CI/CD Implementation Steps
- created stocks-devops repo on my github account
- initialized git on local stocks-devops directory with `git init`
- linked local git with github using `git remote add origin <url>`
- created basic jenkinsfile and develop branch (off of master)
    - using `agent any` to run builds on master for proof of concept
- ran containerized jenkins according to dockerhub image instructions
    - https://github.com/jenkinsci/docker/blob/master/README.md
    - `docker run -d -v jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts-jdk11`
- created multibranch pipeline with github push/PR as trigger
    - had to install docker pipeline plugin (not installed with "recommended" plugins)
    - used ngrok to create a public IP for jenkins container's host so github can push notifications and trigger builds:
        1. download ngrok https://ngrok.com/download to host
        2. create ngrok free account in website
        3. added ngrok token with `ngrok authtoken <token>`
        4. started a tunnel `nohup ngrok http 8080 --log=stdout > ngrok.log &`
        5. copy paste ngrok url to Manage Jenkins > Configure System > Jenkins Location > Jenkins URL
            - Jenkins should automatically change the webhook url in GH repo to <ngrok url>/github-webhook/
        6. redo steps 4 and 5 every 8 hours (free tier session expire) or when needed
    - steps to make github and jenkins work together:
        1. create a personal access token (PAT) on GH with scopes: repo:*, admin:repo_hook, admin:org_hook
        2. create secret text credentials on Jenkins with PAT as secret (for Jenkins to create webhooks in GH repo)
        3. create username & password credentials on Jenkins with GH username & PAT (for job to pull repo + branches)
        4. Manage Jenkins > Configure System > GitHub > Add GitHub Server: add credentials from step 2, check "Manage Hooks"
        5. create multibranch pipeline using credentials from step 3 and change Discover Branches setting to "All Branches"
- setting up build agent container on jenkins server's container's host machine (https://devopscube.com/docker-containers-as-build-slaves-jenkins/):
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

### Vault

#### Local Server Setup

vault server -dev -dev-listen-address="<ip>:8200"

export VAULT_ADDR='<ip>:8200'
export VAULT_TOKEN='<root_token>'

vault policy write jenkins -<<EOF
path "secret/docker" {
  capabilities = [ "read", "list" ]
}
EOF

vault auth enable approle

vault write auth/approle/role/jenkins token_policies="jenkins" \
    secret_id_ttl=10m \
    token_num_uses=2 \
    token_ttl=20m \
    token_max_ttl=30m \
    secret_id_num_uses=4

vault kv put /secret/docker username="<username>" password="<password>"

vault kv get -output-curl-string secret/docker

#### Jenkins Setup

- installed Vault plugin
- added address of server
- added root token as credential