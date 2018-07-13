pipeline {
    agent {
        label 'slave'
    }

    parameters {
         string(name: 'tomcat_dev', defaultValue: '54.157.252.146', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '54.91.200.57', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
     }

stages{
        stage('Build'){
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

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    agent { label 'master' }
                    steps {
                        sh "scp -i /var/jenkins_home/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat8/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    agent { label 'master' }
                    steps {
                        sh "scp -i /var/jenkins_home/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat8/webapps"
                    }
                }
            }
        }
    }
}
