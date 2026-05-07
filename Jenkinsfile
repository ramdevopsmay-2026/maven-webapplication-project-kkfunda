node {

   echo "git branch name: ${env.JOB_NAME}"
   echo "build number is: ${env.BUILD_NUMBER}"
   echo "node name is: ${env.NODE_NAME}"

    def mavenHome=tool name: "maven-3.9.9"
	// START notification
    slackSend(
        channel: '#jio-dev',
        color: 'YELLOW',
        message: "Job Started: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
        )
    try {

    stage('git checkout')
    {
     git branch: 'dev', url: 'https://github.com/ramdevopsmay-2026/maven-webapplication-project-kkfunda.git'
    }
    stage('compile')
    {
    sh  "${mavenHome}/bin/mvn compile"
    }
    stage('Build')
    {
    sh  "${mavenHome}/bin/mvn clean package"
    }
   stage('SQ Report')
    {
    sh  "${mavenHome}/bin/mvn sonar:sonar"
    }
    stage('Deploy into Nexus')
    {
    sh  "${mavenHome}/bin/mvn clean deploy"
    }
    stage('Deploy to Tomcat') {
    echo "Deploying WAR file using curl..."

    sh """
        curl -u sai:password \
        --upload-file /var/lib/jenkins/workspace/scriptedpl-ci-cd-test/target/maven-web-application.war \
        "http:/13.233.41.182:8080/manager/text/deploy?path=/maven-web-application&update=true"
    """
}

  } //try block end
  catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
    throw e
  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
}  //node block end

def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
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
  slackSend (color: colorCode, message: summary, channel: '#jio-dev')
}
