node{
    sendSlackNotifications('STARTED')
    def mavenHome = tool name: "maven 3.9.0"
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
    echo "job name is: ${env.JOB_NAME}"
    echo "node name is: ${env.NODE_NAME}"
    echo "build number is: ${env.BUILD_NUMBER}"
    try{
    stage('Git/CheckOutCode')
    {
    git branch: 'development', changelog: false, credentialsId: 'bbf196b3-da17-4dd6-8ff9-514a293c7515', poll: false, url: 'https://github.com/SAMBAGMG/maven-web-application.git'
    }
    stage('Maven/Build')
    {
        sh "${mavenHome}/bin/mvn clean package"
    }
    stage('SonarQube/Quality')
    {
        sh "${mavenHome}/bin/mvn clean sonar:sonar"
    }
    stage('Nexus/Artifact')
    {
        sh "${mavenHome}/bin/mvn clean deploy"
    }
    stage ('Tomcat/ApplicationServer')
    {
        sshagent(['841873b8-2d49-4a6f-8147-ae3b730073fb']) {
sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@18.208.231.253:/opt/apache-tomcat-9.0.73/webapps/"
        }
    }
    }catch (e){
 currentBuild.result = "FAILED"
    throw e
    }
    finally{
sendSlackNotifications(currentBuild.result)
}
}
def sendSlackNotifications(String buildStatus = 'STARTED') {
  
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary, channel: 'walmart')
}
