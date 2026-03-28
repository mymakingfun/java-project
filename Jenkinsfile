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
                      
                echo "Hello, ${PROJECT_NAME}!"
            }
                
        }
    }
}