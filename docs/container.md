## Containerization Steps

- created requirements.txt using package
    - ``
- added `if __name__ == '__main__': app.run(debug=True, host='0.0.0.0')` to app so ENTRYPOINT & CMD can be python & stocks-devops
- created Dockerfile based on instructions, best practices, and linter recommendations linked below

## Links

- loose instructions: https://runnable.com/docker/python/dockerize-your-flask-application
- some Dockerfile best practices: https://matduggan.com/are-dockerfiles-good-enough/
- Dockerfile linter: https://github.com/hadolint/hadolint
- Image repo: https://hub.docker.com/repository/docker/luk020/stocks-devops

## Commands

- build app image: `docker build -t luk020/stocks-devops:latest .`
- run app container: `docker run --rm -p 5000:5000 stocks-devops`
- push app image to docker hub: `docker push luk020/stocks-devops:latest`
- remove <none>:<none> images from builds: `docker rmi $(docker images -f "dangling=true" -q)`