/**********************************************************************************
 * JenkinsFile for WorkE
 *
 * for adjusting build number for specific branch build
 * Jenkins.instance.getItemByFullName("WorkE-multibranch/develop").updateNextBuildNumber(185)
 *  
 * CHANGE build.properties TO BUILD AND DEPLOY A NEW BUILD
 *
 ***********************************************************************************/
// [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/MyRobotLab/WorkE/']

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '3')), [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/MyRobotLab/WorkE/'], pipelineTriggers([pollSCM('* * * * *')])])

   
// node ('ubuntu') { // use any node
node ('master') { // use any node

   def version = "0.0.${env.BUILD_NUMBER}" 
   def groupId = "org.myrobotlab"
   def artifactId = "worke"

   // deployment variables
   def path = groupId.replace(".","/") + "/" + artifactId.replace(".","/")
   def repo = "/repo/artifactory/myrobotlab/" + path + "/" 

   stage('clean') { 
      echo 'clean the workspace'
      // deleteDir()
      cleanWs()
   }

   stage('check out') { 
      // checkout scm - apparently below "polls" what is the rate?
      git 'https://github.com/MyRobotLab/WorkE.git'
   }

   stage('build') { 
      sh 'echo \"' + version + '\" > resource/WorkE/version.txt'
   }

   stage('zip') {
   
        sh "zip -r ${artifactId}-${version}.zip resource"
        // archiveArtifacts artifacts: 'test.zip', fingerprint: true    
	}

   /**
    * deployment locally by installing into maven like repo with nginx serving the repo directory
    */
   stage('install') {
      sh "mkdir -p ${repo}${version}"

      sh "cp ${artifactId}-${version}.zip ${repo}${version}"

      // ${artifactId}-{version}.pom
      def depFileName = repo + version + "/" + artifactId + "-" + version + ".pom"
      echo "writing pom " + depFileName
      File file = new File(depFileName)
      file.write("<project>")
      file.append("<modelVersion>4.0.0</modelVersion>")
      file.append("<groupId>"+groupId+"</groupId>")
      file.append("<artifactId>"+artifactId+"</artifactId>")
      file.append("<version>"+version+"</version>")
      file.append("<description>WorkE main service module for InMoov compatible with Nixie release of myrobotlab</description>")
      file.append("<url>http://myrobotlab.org</url>")
      file.append("</project>")

      // sh "cp ${artifactId}-${version}.zip ${repo}latest.release/${artifactId}-latest.release.zip"
     }
}
