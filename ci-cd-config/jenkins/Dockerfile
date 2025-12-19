FROM jenkins/jenkins:lts-jdk17

USER root

# outils + Docker CLI
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates curl gnupg lsb-release git \
 && mkdir -p /etc/apt/keyrings \
 && curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg \
 && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(. /etc/os-release && echo $VERSION_CODENAME) stable" \
    > /etc/apt/sources.list.d/docker.list \
 && apt-get update \
 && apt-get install -y --no-install-recommends docker-ce-cli \
 && rm -rf /var/lib/apt/lists/*

# kubectl
RUN curl -fsSL https://dl.k8s.io/release/stable.txt -o /tmp/k8sver \
 && curl -fsSL "https://dl.k8s.io/release/$(cat /tmp/k8sver)/bin/linux/amd64/kubectl" -o /usr/local/bin/kubectl \
 && chmod +x /usr/local/bin/kubectl \
 && rm -f /tmp/k8sver

USER jenkins

