pipeline {
    agent none

    parameters {
         string(name: 'tomcat_dev', defaultValue: '172.31.93.42', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '172.31.80.69', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
     }

stages{
        stage('Build'){
            agent { label 'slave' }
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        stage('Copy Archive') {
            agent { label 'master' }
            steps {
                 script {
                     step ([$class: 'CopyArtifact',
                     projectName: 'Automated',
                     filter: '**/target/*.war']);
                 }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    agent { label 'master' }
                    steps {
                        sh "scp **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat8/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    agent { label 'master' }
                    steps {
                        sh "scp **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat8/webapps"
                    }
                }
            }
        }
    }
}
