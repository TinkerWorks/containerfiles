#!/usr/bin/env groovy
String daily_cron_string = BRANCH_NAME == "master" ? "@daily" : ""


pipeline {
    agent {
        kubernetes {
            defaultContainer 'buildah'
            yamlFile 'kubepods.yaml'
        }
    }
    triggers { cron(daily_cron_string) }

    stages {
        stage('... Build ...') {
            matrix {
                axes {
                    axis {
                        name 'PROJECT'
                        values 'tinkernextcloud', 'tinkertransmission', 'tinkertasmota'
                    }
                }
                stages {
                    stage('Check') {
                        steps{
                            echo "Project: ${PROJECT}"
                            sh "env"
                            sh "cat /etc/os-release"
                            sh "whoami"
                            sh "buildah info"
                        }
                    }
                    stage('Build') {
                        steps{
                            sh "buildah bud -t ${PROJECT} -f Dockerfile ${PROJECT}"
                        }
                    }
                    stage('Push') {
                        steps{
                            sh "buildah push ${PROJECT} docker://registry.tinker.haus/${PROJECT}:latest"
                        }
                    }
                }
            }
        }
    }
}
