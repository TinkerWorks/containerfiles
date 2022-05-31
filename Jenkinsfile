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
                        when {
                            anyOf {
                                branch 'master'
                                branch 'deploy'
                            }
                        }
                        steps{
                            sh "buildah push ${PROJECT} docker://registry.tinker.haus/${PROJECT}:latest"
                        }
                    }
                }
            }
        }
        stage('... Rollout ...') {
            when {
                anyOf {
                    branch 'master'
                    branch 'deploy'
                }
            }
            steps{
                container('kubectl') {
                    sh '''
                      namespaces=$(kubectl get namespaces | grep -v NAME | grep -v kube | awk "{print $1}")
                      for ns in $namespaces ; do
                        kubectl rollout restart -n $ns deployments
                        kubectl rollout restart -n $ns daemonsets
                      done
                    '''
                }
            }
        }
    }
}
