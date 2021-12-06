FROM ubuntu:18.04

LABEL maintainer="https://github.com/bibinwilson/jenkins-docker-slave/blob/master/Dockerfile"

# Make sure the package repository is up to date.
RUN apt-get update && \
    apt-get -qy full-upgrade && \
    apt-get install -qy git jq
# Install a basic SSH server
RUN apt-get install -qy openssh-server && \
    sed -i 's|session    required     pam_loginuid.so|session    optional     pam_loginuid.so|g' /etc/pam.d/sshd && \
    mkdir -p /var/run/sshd
# Install docker
RUN apt-get install -qy sudo apt-utils ca-certificates curl gnupg lsb-release && \
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
        sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg && \
    echo \
        "deb [arch=$(dpkg --print-architecture) \
	    signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
	    https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
	    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null && \
    apt-get update && \
    apt-get install -qy docker-ce docker-ce-cli containerd.io
# Cleanup old packages
RUN apt-get -qy autoremove && \
# Add user jenkins to the image
    useradd -m jenkins && \
# Set password for the jenkins user (you may want to alter this).
    echo "jenkins:jenkins" | chpasswd && \
    mkdir /home/jenkins/.m2

#ADD settings.xml /home/jenkins/.m2/
# Copy authorized keys
COPY .ssh/authorized_keys /home/jenkins/.ssh/authorized_keys

RUN chown -R jenkins:jenkins /home/jenkins/.m2/ && \
    chown -R jenkins:jenkins /home/jenkins/.ssh/

# Standard SSH port
EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]