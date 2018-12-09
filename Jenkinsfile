pipeline {
    agent any

    parameters {
         string(name: 'tomcat_dev', defaultValue: '3.80.194.50', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '18.205.29.152', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
     }

    tools { 
        maven 'localMaven'
        jdk 'localJDK'
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
                        sh "scp -o StrictHostKeyChecking=no -i /home/rajith/Documents/AWS/Deployments/pocker/pockerserver.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -o StrictHostKeyChecking=no -i /home/rajith/Documents/AWS/Deployments/pocker/pockerserver.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}