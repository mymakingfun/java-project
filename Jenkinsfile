pipeline {
    agent {
        docker {
            image 'maven:3.9.14-eclipse-temurin-25'
            args '-u 111:113 -v /var/lib/jenkins/.m2:/var/lib/jenkins/.m2'
        }
    }
    environment {
        PROJECT_NAME = 'java-project'
        GIT_COMMIT = ''
    }
    stages {
        stage ('Checkout') {
            steps {
                checkout scm
                script {
                    GIT_COMMIT = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                }
            }

        }
        stage ('Build') {
            steps {
                script {
                    githubNotify context: 'Jenkins CI', 
                        description: "Building...", 
                        status: 'PENDING',
                        credentialsId: 'github-https',
                        repo: 'mymakingfun/java-project',
                        sha: env.GIT_COMMIT
                }

                echo "Hello, ${PROJECT_NAME}!"
            }       
        }
    }
    post {
        always {
            script {
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
                        repo: 'mymakingfun/java-project',
                        sha: env.GIT_COMMIT
            }
        }
    }
}