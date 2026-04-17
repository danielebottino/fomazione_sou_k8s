FROM ubuntu

USER root

# Install Docker CLI
RUN apt-get update && \
    apt-get install -y docker.io docker-compose-plugin Jenkins && \
    rm -rf /var/lib/apt/lists/*

# Add Jenkins user to docker group
RUN usermod -aG docker jenkins

USER jenkins

