node {

    def mavenHome = tool name: "maven394"
  
		stage("1.GetCode"){
    		git branch: 'main', url: 'https://github.com/benadaba/maven-web-application.git'
    }
    stage("2.CleanWorkspace"){
      sh "${mavenHome}/bin/mvn clean"
    } 
    stage("3.Unittest"){ 
     sh "${mavenHome}/bin/mvn test"
    }
    
    stage("4.BuildProject"){
    sh "${mavenHome}/bin/mvn package"
    }
    stage("5.CodeQualityQuality"){
     sh "${mavenHome}/bin/mvn sonar:sonar"
    }
    stage("6.UploadArtifacts"){
     sh "${mavenHome}/bin/mvn deploy"
    }
    
    stage("7.DeployToUAT_tomcatAppserver"){
      deploy adapters: [tomcat9(credentialsId: 'tomcat-cred', path: '', url: 'http://13.42.18.174:8080/')], contextPath: null, war: 'target/*.war'   
    }
    
    stage("8.ApprovalGate"){
      timeout(time:1, unit:'MINUTES'){
        input message: "Please review the application and give us your feedback before we take the next action"
      }
    }
    
    stage("9.DeployToPRODAppserver"){
      deploy adapters: [tomcat9(credentialsId: 'tomcat-cred', path: '', url: 'http://35.179.106.255:8080/')], contextPath: null, war: 'target/*.war'   
    }
    stage("10.SendNotification"){
    emailext body: '''We are building the full enterprise project for barclays bank plc. This the production pipeline. Please check the progress of this pipeline in this email.

Thank
DataPandas DevOps Consultant''', recipientProviders: [buildUser(), culprits(), developers(), brokenBuildSuspects()], subject: 'Please check progress of the build', to: 'barcalys@datapandas.com'
    }
   
}
