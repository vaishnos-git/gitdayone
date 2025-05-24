node
{
   echo "git branch name: ${env.BRANCH_NAME}"
   echo "build number is: ${env.BUILD_NUMBER}"
   echo "node name is: ${env.NODE_NAME}"
    def mavenhome=tool name :"maven-3.9.9"
    try 
    {
    notifyBuild('STARTED')
    stage('git checkout')
    {
        git branch: 'main', credentialsId: '55d916b1-b055-435f-b5fe-318aaf445e5c', url: 'https://github.com/vaishnos-git/gitdayone.git'
    }
    stage('compile')
    {
        sh "${mavenhome}/bin/mvn clean compile"
    }
    stage('BUILD')
    {
        sh "${mavenhome}/bin/mvn clean package"
    }
    stage('sonar-r')
    {
        sh "${mavenhome}/bin/mvn clean sonar:sonar"
    }
    stage('nexus')
    {
        sh "${mavenhome}/bin/mvn clean deploy"
    }
    }
    catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
    throw e
  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
}

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
  slackSend (color: colorCode, message: summary)
}
