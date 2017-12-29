pipeline {
  agent any

  tools {
    maven 'localMaven'
  }

  parameters {
    string(name: 'tomcat_dev', defaultValue: '54.68.112.64', description: 'Staging Server')
    string(name: 'tomcat_prod', defaultValue: '52.36.130.36', description: 'Production Server')
  }

  triggers {
    pollSCM('H/5 * * * *')
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
                    steps {
                        sh "scp -i ~/Downloads/tomcat-jenkins-demo.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i ~/Downloads/tomcat-jenkins-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }


}
