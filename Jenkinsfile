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
        stage('Check') {
            steps{
                sh """
                     env
                     cat /etc/os-release
                     whoami
                     buildah info
                """
            }
        }
        stage('BUILD & PUSH & ROLLOUT') {
            parallel {
                stage('build & push') {
                    steps {
                        script {
                            dir('dockerfiles') {
                                images = []
                                files = findFiles()
                                files.each { f ->
                                    if (f.directory) {
                                        images.add(f.name)
                                    }
                                }
                            }
                        }
                        runParallel items: images.collect { "${it}" }
                    }
                }
            }
        }
    }
}

def runParallel(args) {
    parallel args.items.collectEntries { name -> [ "${name}": {
        stage("${name}") {
            dir("dockerfiles/${name}") {
                stage("Check ${name}") {
                    sh "env"
                    sh "pwd"
                    sh "find"
                }
                stage("Build ${name}") {
                    sh "buildah bud -t ${name} -f Dockerfile ."
                }
                stage("Push ${name}") {
                    if (env.BRANCH_NAME == 'master') {
                        sh "buildah push ${name} docker://registry.tinker.haus/${name}:latest"
                    }
                }
                container('kubectl') {
                    stage("Rollout ${name}") {
                        if (env.BRANCH_NAME == 'master') {
                            sh """
                              namespace=`kubectl get deployments -A | grep ${name} | head -n1 | awk '{print \$1}'`
                              deployments=`kubectl get deployments -A | grep ${name} | awk '{print \$2}'`
                              for dp in \${deployments} ; do
                                kubectl rollout restart -n \${namespace} deployment \${dp}
                              done
                            """
                        }
                    }
                }
            }
        }
    }]}
}
