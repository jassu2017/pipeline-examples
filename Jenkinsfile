
node('master')
{
   def mvnHome
   stage('CHeckOut') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/jassu2017/Java-Mysql-Simple-Login-Web-application.git'
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'Jenkins_Maven'
   }
   
  stage('Sonarqube analysis') {
		withSonarQubeEnv('Sonarqube') {
		bat(/"${mvnHome}\bin\mvn" sonar:sonar \
		          -Dsonar.login=a5c92ac4c180b534f4556f7c576ebed54c8758b8/)
		}
		}
		
	stage('Quality Gate'){
	    timeout(time: 5, unit: 'MINUTES') { // Just in case something goes wrong, pipeline will be killed after a timeout
	    sleep(10)
    def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
    if (qg.status != 'OK') {
      error "Pipeline aborted due to quality gate failure: ${qg.status}"
    }
  }
	}
   stage('Build') {
      // Run the maven build
      if (isUnix()) {
         sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
      } else {
         bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
      }
	   stage ('send email') {
		   //some code
	   }
   

    
}
