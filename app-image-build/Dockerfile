# SHA is the only way to truly know the same image is being ran every time
FROM ubuntu@sha256:7cc0576c7c0ec2384de5cbf245f41567e922aab1b075f3e8ad565f508032df17 

# running apt-get upgrade defeats the purpose of repeatable builds
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3.8 python3-pip \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
# copying script after installing requirements to avoid breaking caching functionality
COPY ./app-image-build/requirements.txt .

RUN pip3 install -r requirements.txt

# switching to non-root user here because root privileges needed for apt and pip installs
RUN useradd -u 8888 devops
USER devops

COPY ./app .

EXPOSE 5000/tcp

# https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact
ENTRYPOINT [ "python3" ]

CMD [ "stocks-devops.py" ]