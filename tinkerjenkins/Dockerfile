FROM docker.io/jenkins/jenkins:latest
COPY --chown=jenkins:jenkins plugins.yaml /usr/share/jenkins/ref/plugins.yaml
RUN jenkins-plugin-cli -f /usr/share/jenkins/ref/plugins.yaml
