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
                        values 'nextcloud', 'transmission', 'pushgetserver', 'jenkins'
                    }
                }
                stages {
                    stage('Check') {
                        steps{
                            echo "Project: ${PROJECT}"
                            sh """
                              env
                              cat /etc/os-release
                              whoami
                              buildah info
                            """
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
