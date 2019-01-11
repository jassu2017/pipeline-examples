node('master')
{
   def mvnHome
   try {
   notifyStarted()
   stage('CHeckOut') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/jassu2017/Java-Mysql-Simple-Login-Web-application.git'
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'Jenkins_Maven'
   }
   echo 'checkout completed'
   echo '******************************************---------------------------'
   
   stage('Sonarqube analysis') {
		withSonarQubeEnv('Sonarqube') {
		bat(/"${mvnHome}\bin\mvn" sonar:sonar \
		          -Dsonar.login=a5c92ac4c180b534f4556f7c576ebed54c8758b8/)
		}
		}
	echo 'Sonarqube Analysis Done'
	   echo '------------------------------------------------------------'
	
	stage('Quality Gate'){
	    timeout(time: 5, unit: 'MINUTES') { // Just in case something goes wrong, pipeline will be killed after a timeout
	    sleep(10)
    def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
    if (qg.status != 'OK') {
      error "Pipeline aborted due to quality gate failure: ${qg.status}"
    }
  }
	}
	echo 'Quality Gate Sucessfull'
	   echo '------------------------------------------------------------'
	
	stage('Build') {
      // Run the maven build
      if (isUnix()) {
         sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
      } else {
         bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean install/)
      }
   }
   echo 'Build Done'
	   echo '------------------------------------------------------------'
   
   	stage('Upload Archive to Artifactory ')
 {
  // Get Artifactory server instance, defined in the Artifactory Plugin
     def server = Artifactory.server('artifactory-1')      
	 
 // Read the upload spec and upload files to Artifactory. 
	 def uploadSpec = """{
        "files": [
    {
      "pattern": "**/*.war",
      "target": "libs-release-local/"
    }
                  ]
                         }"""
    server.upload(uploadSpec)

         // Upload to Artifactory.
    def buildInfo2 = server.upload(uploadSpec)

         // Publish the build to Artifactory
    server.publishBuildInfo buildInfo2
	 
}
    echo 'Artifacts archived to Artifactory'
     echo '------------------------------------------------------------'
    stage ('Deploy')
     {
    build 'Deploy_to_QA'
      }
   
   notifySuccessful()
   } catch (e) {
     currentBuild.result = "FAILED"
     notifyFailed()
     throw e
   }
   
}
def notifyStarted() {
       emailext ( 
       subject: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'", 
       body: """<p>STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
         <p>Check console output at "<a href="${env.BUILD_URL}">${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>"</p>""",
       to:'jayshree.prasad@live.com'
     )
   }
   
def notifySuccessful() {
    emailext (
       subject: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
       body: """<p>SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
         <p>Check console output at "<a href="${env.BUILD_URL}">${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>"</p>""",
       to:'jayshree.prasad@live.com'
     )
}
def notifyFailed() {
    emailext (
       subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
       body: """<p>FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
         <p>Check console output at "<a href="${env.BUILD_URL}">${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>"</p>""",
       to:'jayshree.prasad@live.com'
     )
 }
 
   
  
