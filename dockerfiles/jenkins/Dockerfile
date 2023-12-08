FROM docker.io/jenkins/jenkins:jdk21
COPY --chown=jenkins:jenkins plugins.yaml /usr/share/jenkins/ref/plugins.yaml
RUN jenkins-plugin-cli -f /usr/share/jenkins/ref/plugins.yaml
