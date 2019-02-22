pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: 'ec2-13-211-209-73.ap-southeast-2.compute.amazonaws.com', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: 'ec2-52-65-211-12.ap-southeast-2.compute.amazonaws.com', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
     }

stages{
        stage('Build'){
            steps {
                bat 'mvn clean package'
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
                        sh "scp -i /usr/lib/jenkins/sydney-ec2-key1.pem **/target/*.war ec2-user@${params.tomcat_dev}:/usr/share/tomcat8/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /usr/lib/jenkins/sydney2.pem **/target/*.war ec2-user@${params.tomcat_prod}:/usr/share/tomcat8/webapps"
                    }
                }
            }
        }
    }
}
