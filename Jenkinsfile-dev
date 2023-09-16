node{
      def mavenHome = tool name: "maven386"
     stage("1GetCode"){
        git branch: 'main', url: 'https://github.com/benadaba/maven-web-application'   
     }
     
     
     stage("2Test+Build"){
       sh "${mavenHome}/bin/mvn clean package" 
     }
     
     
     stage("3CodeQualityAnalysis"){
       sh "${mavenHome}/bin/mvn sonar:sonar"
     }
     
     
     stage("4UploadArtifacts"){
     
     	 sh "${mavenHome}/bin/mvn deploy"
     }
     
     stage("5DeploytoUAT_tomcatappserver"){
        deploy adapters: [tomcat9(credentialsId: 'TOMCAT-CREDENTIALS', path: '', url: 'http://13.40.82.33:8080/')], contextPath: null, onFailure: false, war: 'target/*war'
     
     }
     
     stage("6.ApprovalGate"){
     		timeout(time:1, unit:'HOURS') {
   					 input message: "Please review the application and give feedback for Go-No-Go"
				}
     }
     
      stage("7DeploytoPROD"){
        deploy adapters: [tomcat9(credentialsId: 'TOMCAT-CREDENTIALS', path: '', url: 'http://13.40.82.33:8080/')], contextPath: null, onFailure: false, war: 'target/*war'
     
     }
     
     stage("8Notifications"){
       emailext body: '''Hi All, 
        Please find the status of the project

        Regards
        DataPandas''', recipientProviders: [buildUser(), contributor(), culprits(), developers(), requestor()], subject: 'CompareTheMarket Project build status', to: 'devopsengineeers@datapandas.com'
     }
     
  }
