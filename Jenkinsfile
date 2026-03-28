pipeline {
    agent {
        docker {
            image 'maven:3.9.14-eclipse-temurin-25'
            args '-u 111:113 -v /var/lib/jenkins/.m2:/var/lib/jenkins/.m2'
        }
    }
    environment {
        PROJECT_NAME = 'java-project'
    }
    stages {
        stage ('hello') {
            steps {
                script {
                    githubNotify context: 'Jenkins CI', description: "Building...", status: 'PENDING'
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
                githubNotify context: 'Jenkins CI', status: githubStatus, description: "Build finished"
            }
        }
    }
}