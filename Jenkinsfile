pipeline {
    agent {
        label 'inbound-agent'
    }
    stages {
        stage('Main分支判断') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        echo '当前为 main 分支，执行 main 分支专属流程'
                        // main分支专属逻辑
                        sh "mvn clean package"
                        
                    } else {
                        // pull request 或 其他分支都需要自动加SNAPSHOT
                        echo "当前为Pull Request或其他分支：${env.BRANCH_NAME}，自动处理版本为SNAPSHOT"
                        def currentVersion = sh(script: 'mvn help:evaluate -Dexpression=project.version -q -DforceStdout', returnStdout: true).trim()
                        echo "当前pom版本: ${currentVersion}"
                        def newVersion = currentVersion.endsWith('-SNAPSHOT') ? currentVersion : currentVersion + '-SNAPSHOT'
                        echo "新版本: ${newVersion}"
                        sh "mvn versions:set -DnewVersion=${newVersion} -DgenerateBackupPoms=false"

                        sh "mvn clean package"
                        if (env.CHANGE_ID) {
                            echo 'Pull Request流程'
                            
                        } else {
                            echo '其他分支流程'
                            
                        }
                        
                    }
                    
                }
            }
        }
    }
}