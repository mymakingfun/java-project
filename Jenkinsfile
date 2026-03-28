pipeline {
    agent {
        docker {
            image 'maven:3.9.14-eclipse-temurin-25'
            args '-u 111:113 -v /var/lib/jenkins/.m2:/var/lib/jenkins/.m2'
        }
    }
    environment {
        PROJECT_NAME = 'java-project'
        GITHUB_ACCOUNT = 'mymakingfun'
        GITHUB_REPO = 'java-project'
        COMMIT_SHA = ''
    }
    stages {
        stage ('Checkout') {
            steps {
                script {
                    def scmVars = checkout scm
                    env.COMMIT_SHA = (scmVars?.GIT_COMMIT ?: env.GIT_COMMIT ?: '').trim()

                    if (!(env.COMMIT_SHA ==~ /[0-9a-f]{40}/)) {
                        env.COMMIT_SHA = sh(script: 'git rev-parse HEAD || true', returnStdout: true).trim()
                    }

                    echo "Resolved commit sha: ${env.COMMIT_SHA}"
                }
            }

        }
        stage ('Build') {
            steps {
                script {
                    if (env.COMMIT_SHA ==~ /[0-9a-f]{40}/) {
                        githubNotify context: 'Jenkins CI',
                            description: "Building...",
                            status: 'PENDING',
                            credentialsId: 'github-https',
                            account: env.GITHUB_ACCOUNT,
                            repo: env.GITHUB_REPO,
                            sha: env.COMMIT_SHA
                    } else {
                        echo "Skip githubNotify(PENDING): invalid COMMIT_SHA='${env.COMMIT_SHA}'"
                    }
                }

                echo "Hello, ${PROJECT_NAME}!"
            }       
        }
    }
    post {
        always {
            script {
                if (!(env.COMMIT_SHA ==~ /[0-9a-f]{40}/)) {
                    env.COMMIT_SHA = (env.GIT_COMMIT ?: '').trim()
                }

                if (!(env.COMMIT_SHA ==~ /[0-9a-f]{40}/)) {
                    env.COMMIT_SHA = sh(script: 'git rev-parse HEAD || true', returnStdout: true).trim()
                }

                if (!(env.COMMIT_SHA ==~ /[0-9a-f]{40}/)) {
                    echo "Skip githubNotify(FINAL): invalid COMMIT_SHA='${env.COMMIT_SHA}'"
                    return
                }

                def githubStatus = 'SUCCESS'
                if (currentBuild.currentResult == 'FAILURE') {
                    githubStatus = 'FAILURE'
                } else if (currentBuild.currentResult == 'UNSTABLE') {
                    githubStatus = 'ERROR'
                } else if (currentBuild.currentResult == 'ABORTED') {
                    githubStatus = 'ERROR'
                }
                githubNotify context: 'Jenkins CI', 
                        description: "Build finished", 
                        status: githubStatus,
                        credentialsId: 'github-https',
                        account: env.GITHUB_ACCOUNT,
                        repo: env.GITHUB_REPO,
                        sha: env.COMMIT_SHA
            }
        }
    }
}