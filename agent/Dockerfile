FROM jenkins/inbound-agent:jdk17

USER root

ENV SONAR_SCANNER_VERSION=5.0.1.3006

RUN apt-get update && \
    apt-get install -y \
    git \
    docker.io \
    curl \
    unzip \
    wget \
    nodejs \
    npm \
    && rm -rf /var/lib/apt/lists/*

RUN wget -q https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SONAR_SCANNER_VERSION}-linux.zip -O /tmp/sonar-scanner.zip && \
    unzip -q /tmp/sonar-scanner.zip -d /opt/ && \
    rm /tmp/sonar-scanner.zip && \
    ln -s /opt/sonar-scanner-${SONAR_SCANNER_VERSION}-linux/bin/sonar-scanner /usr/local/bin/sonar-scanner

USER jenkins
