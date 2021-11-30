## CI/CD Implementation Steps
- created stocks-devops repo on my github account
- initialized git on local stocks-devops directory with `git init`
- linked local git with github using `git remote add origin <url>`
- created basic jenkinsfile and develop branch (off of master)
    - using `agent any` since using docker agent inside jenkins container requires extra research
- ran containerized jenkins according to dockerhub image instructions
    - https://github.com/jenkinsci/docker/blob/master/README.md
    - `docker run -d -v jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts-jdk11`
- created multibranch pipeline with github push/PR as trigger
    - had to install docker pipeline plugin (not installed with "recommended" plugins)
    - used ngrok to create a public IP for jenkins container's host so github can push notifications and trigger builds:
        1. download ngrok https://ngrok.com/download to host
        2. create ngrok free account in website
        3. added ngrok token with `ngrok authtoken <token>`
        4. started a tunnel `ngrok http 8080`
        5. copy paste ngrok url to Manage Jenkins > Configure System > Jenkins Location > Jenkins URL
            - Jenkins should automatically change the webhook url in GH repo to <ngrok url>/github-webhook/
        6. redo steps 4 and 5 every 8 hours (free tier session expire) or when needed
    - steps to make github and jenkins work together:
        1. create a personal access token (PAT) on GH with scopes: repo:*, admin:repo_hook, admin:org_hook
        2. create secret text credentials on Jenkins with PAT as secret (for Jenkins to create webhooks in GH repo)
        3. create username & password credentials on Jenkins with GH username & PAT (for job to pull repo + branches)
        4. Manage Jenkins > Configure System > GitHub > Add GitHub Server: add credentials from step 2, check "Manage Hooks"
        5. create multibranch pipeline using credentials from step 3 and change Discover Branches setting to "All Branches"