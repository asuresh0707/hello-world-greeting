node('docker'){
   stage('poll scm') {
     scm checkout
   }
   stage('build and unit test') {
       sh 'mvn clean verify -DskipITs=true';
       junit '**/targets/surefire-reports/TEST-*.xml'
      archive 'target/*.jar'
   }
   stage('static code analysis'){
      sh 'mvn clean verify sonar:sonar -Dsonar.projectName=example-project -Dsonar.projectKey=example-project 
      -Dsonar.projectVersion=$BUILD_NUMER';
   }
   stage('integration-test'){
      sh 'mvn clean verify -Dsurefire.skip=true';
      junit '**/targets/failsafe-reports/TEST-*.xml'
      archive '/targets/*.jar'
   }
   stage('publish to artifactory'){
      def server = Artifactory.server 'default artifactory server'
      def uploadSpec = """ {
         "files" : [
             "patteren" : "target/hello-0.0.1.war",
             "target" : "hello-world-greeing-project/${BUILD_NUMBER}/"
             "props" : "Integratio-Tested=yes;Performannce-Tested=no"
         ]
      } """   
      server.upload(uploadSpec)
   }
}
