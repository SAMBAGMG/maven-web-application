node{
    def mavenHome = tool name: "maven 3.9.0"
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
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
sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@10.42.2.119:/opt/apache-tomcat-9.0.73/webapps/"
        }
    }
}
