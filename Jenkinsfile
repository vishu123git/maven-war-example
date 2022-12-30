pipeline{
    agent any
    options {
        buildDiscarder(logRotator(numToKeepStr: '5',daysToKeepStr: '15', artifactNumToKeepStr: '',artifactDaysToKeepStr: ''))
    }
    stages{
        stage('Build using Maven'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('Deploy on Tomcat server'){
            steps{
               sshagent(['tomcat-dev']) {
                   //copy war file on tomcate server
                   sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@172.31.43.203:/opt/tomcat8/webapps/'
                   //stop tomcat service
                   sh 'ssh ec2-user@172.31.43.203 /opt/tomcat8/bin/shutdown.sh'
                   //start tomcat service
                   sh 'ssh ec2-user@172.31.43.203 /opt/tomcat8/bin/startup.sh'
               }
            }
        }
    }
    post{
        success {
            archiveArtifacts artifacts: 'target/*.war'
            cleanWs()
        }
    }
}
