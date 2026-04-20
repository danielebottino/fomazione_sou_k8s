FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# Install base tools
RUN apt-get update && apt-get install -y \
    ca-certificates curl gnupg lsb-release sudo openjdk-21-jdk supervisor && \
    rm -rf /var/lib/apt/lists/*

# -------------------------
# Install Docker Engine
# -------------------------
RUN install -m 0755 -d /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
    gpg --dearmor -o /etc/apt/keyrings/docker.gpg && \
    chmod a+r /etc/apt/keyrings/docker.gpg && \
    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
      https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
      > /etc/apt/sources.list.d/docker.list && \
    apt-get update && \
    apt-get install -y docker-ce docker-ce-cli containerd.io && \
    rm -rf /var/lib/apt/lists/*

# -------------------------
# Install Jenkins WAR (stable)
# -------------------------
RUN curl -fsSL https://get.jenkins.io/war-stable/latest/jenkins.war -o /usr/share/jenkins.war

# Create Jenkins user
RUN useradd -m -d /var/jenkins_home -s /bin/bash jenkins && \
    mkdir -p /var/jenkins_home && \
    chown -R jenkins:jenkins /var/jenkins_home

# Add Jenkins to docker group
RUN groupadd -f docker && usermod -aG docker jenkins

# -------------------------
# Supervisor config
# -------------------------
RUN mkdir -p /var/log/supervisor
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

EXPOSE 8080 50000

CMD ["/usr/bin/supervisord"]
