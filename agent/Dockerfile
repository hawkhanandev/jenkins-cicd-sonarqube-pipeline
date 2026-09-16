FROM jenkins/inbound-agent:jdk17

USER root

RUN apt-get update && \
    apt-get install -y \
    git \
    docker.io \
    curl \
    unzip \
    nodejs \
    npm \
    && rm -rf /var/lib/apt/lists/*

USER jenkins
