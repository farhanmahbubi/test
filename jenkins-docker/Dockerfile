FROM jenkins/jenkins:lts

USER root

RUN apt-get update && apt-get install -y \
    apt-transport-https ca-certificates curl gnupg2 software-properties-common lsb-release \
    && curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add - \
    && add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable" \
    && apt-get update \
    && apt-get install -y docker-ce-cli

RUN groupadd -for docker && usermod -aG docker jenkins

USER jenkins
