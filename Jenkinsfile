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
        stage('foo') {
            parallel(
		        'copy-1': {
                    sh "echo copy1"
		        },
		        'copy-2': {
                    sh "echo copy2"
		        },
		        'some other process': {
			        parallel(
				        'branch-1': {
                            sh "echo copy3 - b1"
				        },
				        'branch-2': {
                            sh "echo copy3 - b2"
				        }
			        )
		        }
	        )
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
        stage('... Rollout ...') {
            when {
                anyOf {
                    branch 'master'
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
                    if (env.BRANCH_NAME == 'master') {
                        sh "buildah bud -t ${name} -f Dockerfile ."
                    }
                    sh "echo build ${name}"
                }
                stage("Push ${name}") {
                    if (env.BRANCH_NAME == 'master') {
                        sh "buildah push ${name} docker://registry.tinker.haus/${name}:latest"
                    }
                    parallel linux: {
                        sh 'echo check 1'
                    },
                        windows: {
                        sh 'echo check 2'
                    }

                }
            }
        }
    }]}
}
