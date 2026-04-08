pipeline {
    agent {
        label 'inbound-agent'
    }
    stages {
        stage('Build and package') {
            steps {
                script {
                    configFileProvider([configFile(fileId: 'maven_settings', variable: 'MAVEN_SETTINGS')]) {
                        if (env.BRANCH_NAME == 'main') {
                            echo '当前为 main 分支，执行 main 分支专属流程'
                            // main分支专属逻辑
                            sh "mvn clean package --settings ${MAVEN_SETTINGS}"
                            
                        } else {
                            // pull request 或 其他分支都需要自动加SNAPSHOT
                            echo "当前为Pull Request或其他分支：${env.BRANCH_NAME}，自动处理版本为SNAPSHOT"
                            def currentVersion = sh(script: 'mvn help:evaluate -Dexpression=project.version -q -DforceStdout --settings ${MAVEN_SETTINGS}', returnStdout: true).trim()
                            echo "当前pom版本: ${currentVersion}"
                            def newVersion = currentVersion.endsWith('-SNAPSHOT') ? currentVersion : currentVersion + '-SNAPSHOT'
                            echo "新版本: ${newVersion}"
                            sh "mvn versions:set -DnewVersion=${newVersion} -DgenerateBackupPoms=false --settings ${MAVEN_SETTINGS}"

                            sh "mvn clean package --settings ${MAVEN_SETTINGS}"
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
}