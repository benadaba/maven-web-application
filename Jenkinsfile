node {
    //tool name: 'maven3910', type: 'maven'
    def mavenHome = tool name: "maven3910"

     stage("01. GetCode"){ 
        git branch: 'main', url: 'https://github.com/benadaba/maven-web-application.git'
        }

     stage("02. Clean"){
        sh "${mavenHome}/bin/mvn clean"
     }
     stage("03. Validate"){
        sh "${mavenHome}/bin/mvn validate"
        //sh "${mavenHome}/bin/mvn -B -DskipTests=true -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true -Djacoco.skip=true -Dfindbugs.skip=true -Dmaven.test.failure.ignore=true clean verify"
     }
     stage("04. Test(UnitTest+Functional+RegressionTest)"){
        sh "${mavenHome}/bin/mvn test"
     }
     stage("05. Build"){
        sh "${mavenHome}/bin/mvn package"
        //sh "${mavenHome}/bin/mvn -B -DskipTests=true -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true -Djacoco.skip=true -Dfindbugs.skip=true -Dmaven.test.failure.ignore=true clean package"
     }
     stage("06. Integration (mvn verify)"){
        sh "${mavenHome}/bin/mvn verify"
        //sh "${mavenHome}/bin/mvn -B -DskipTests=true -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true -Djacoco.skip=true -Dfindbugs.skip=true -Dmaven.test.failure.ignore=true clean verify"
     }
     stage("07. CodeQualityAnalysis"){
        sh "${mavenHome}/bin/mvn sonar:sonar"
        //  sh "${mavenHome}/bin/mvn sonar:sonar -Dsonar.projectKey=maven-web-application -Dsonar.host.url=http://localhost:9000 -Dsonar.login=admin -Dsonar.password=admin"
     }
     stage("08. UploadArtifacts"){
        sh "${mavenHome}/bin/mvn deploy"
     }
     stage("09. DeployToApplicationServer-DEV"){
        deploy adapters: [tomcat8(alternativeDeploymentContext: '', credentialsId: 'tomcat-credentials', path: '', url: 'http://35.179.182.227:8080/')], contextPath: null, onFailure: false, war: 'target/*war'
     }

      stage("10. DeployToApplicationServer-STAGING"){
        deploy adapters: [tomcat8(alternativeDeploymentContext: '', credentialsId: 'tomcat-credentials', path: '', url: 'http://35.179.182.227:8080/')], contextPath: null, onFailure: false, war: 'target/*war'
     }
     stage("11. ApprovalGate"){
        timeout(activity: true, time: 30, unit: 'SECONDS') {
             input message: "Do you want to proceed with the deployment to Production?", ok: "Yes, proceed"
        }
        
     }
    
    stage("12. DeployToApplicationServer-PROD"){
        deploy adapters: [tomcat8(alternativeDeploymentContext: '', credentialsId: 'tomcat-credentials', path: '', url: 'http://35.179.182.227:8080/')], contextPath: null, onFailure: false, war: 'target/*war'
     }

     stage("10. Notifications to stakeholders"){
        emailext body: '''We are building the full enterprise project for barclays bank plc. This the production pipeline. Please check the progress of this pipeline in this email.

        Thank
        DataPandas DevOps Consultant''', recipientProviders: [buildUser(), culprits(), developers(), brokenBuildSuspects()], subject: 'Please check progress of the build', to: 'barcalys@datapandas.com'
    }
     }
