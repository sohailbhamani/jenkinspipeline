pipeline {
    tools {
        maven 'localMaven'
    }    
    
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: '192.168.0.3', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '54.201.71.85', description: 'Production Server')
    } 

    triggers {
         pollSCM('0,30 * * * *') // Polling Source Control
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
                        sh "scp -i ~/jenkins-demo.pem **/target/*.war vagrant@${params.tomcat_dev}:/opt/tomcat/webapps"
                    }
                }
                stage ('Deploy to Production'){
                    steps {
                        sh "scp -i ~/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }

    }
}
