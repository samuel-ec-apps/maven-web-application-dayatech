node{
def mavenHome = tool name: "maven3.8.4"
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

stage('CheckoutCode'){
git branch: 'development', credentialsId: '9de00cc6-fa9e-4070-9c53-12d90d9ac130', 
url: 'https://github.com/samuel-ec-apps/maven-web-application-dayatech.git'
}

stage('Build'){
sh "${mavenHome}/bin/mvn clean package"
}

stage('ExecuteSonarQubeReport'){
sh "${mavenHome}/bin/mvn clean sonar:sonar"
}

stage('UploadArtifactsIntoNexus'){
sh "${mavenHome}/bin/mvn clean deploy"
}

stage('DeployApplicationIntoTomcatServer'){
sshagent(['4929ce2a-d1f1-43ce-8f46-38b75754bd10']) {
    sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.232.229.20:/opt/apache-tomcat-9.0.65/webapps/"
}
}

stage('SendEmailNotification'){
emailext body: '''The Build was Successful.

Regards,
DayaTech''', subject: 'Build Successfull', to: 'samuel.polagani@gmail.com'
}



}
