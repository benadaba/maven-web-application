node {

    def mavenHome = tool name: "maven3910" 
    
    //get the code from git repo
    stage("01. GetCode"){
        git branch: 'main', url: 'https://github.com/benadaba/maven-web-application.git'
    }

     // clean the targetDirect
     stage("02. cleanTarget"){
         sh "${mavenHome}/bin/mvn clean"
     }

     //validate the project
     stage("03. Validate"){
        sh "${mavenHome}/bin/mvn validate"
     }

     //unit testting 
    stage("04. Test(Unittest)"){
        sh "${mavenHome}/bin/mvn test"
    }
   
   // Build the project
    stage("05. Build"){
        sh "${mavenHome}/bin/mvn package"
    }

    // functional test: it verifes that a specific feature or functionality of the application
    // works as expected from the users' perspective. it checks what the system does not how it does
        stage("06. Test(FunctionalTest)"){
            sh "${mavenHome}/bin/mvn verify"
        }
 // integration test: this test checkous  how the different parts of the application work together
        stage("07. IntegrationTest"){
            sh "${mavenHome}/bin/mvn verify"
        }

        //regression test: rerunning previously executed tests to ensure that recent code changes
        // havent broken existing functionality
        stage("08. Test(RegressionTest)"){
            sh "${mavenHome}/bin/mvn test"
            //  sh "${mavenHome}/bin/mvn test -Dtest=RegressionTestSuite"
        }

        //codequality analysis
         stage("09. CodeQualityAnalysis"){
            sh "${mavenHome}/bin/mvn sonar:sonar"
         }

         //upload artifacts to nexus 
         stage("10. UploadArtifacts"){
            sh "${mavenHome}/bin/mvn deploy"
         }

       stage("11. DeploytoApplicationServer-DEV"){
          deploy adapters: [tomcat8(alternativeDeploymentContext: '', credentialsId: 'tomcat-credentials', path: '', url: 'http://13.42.103.12:8080/')], contextPath: null, war: 'target/*.war'
       }

       stage("12. DeploytoApplicationServer-STAGING"){
         deploy adapters: [tomcat8(alternativeDeploymentContext: '', credentialsId: 'tomcat-credentials', path: '', url: 'http://13.42.103.12:8080/')], contextPath: null, war: 'target/*.war'
       }

      //continuous delivery approval gate
      stage("13. ApprovalGate"){
          timeout(time: 30, unit: 'SECONDS') {
                    input message: "Do you want to proceed to Production", ok: "Yes, Proceed"
                }
      }

      stage("14. DeploytoApplicationServer-PROD"){
         deploy adapters: [tomcat8(alternativeDeploymentContext: '', credentialsId: 'tomcat-credentials', path: '', url: 'http://13.42.103.12:8080/')], contextPath: null, war: 'target/*.war'
       }

       stage("15. Notifications to stakeholders"){
      emailext body: 'Please check this email and act accordingly', 
            recipientProviders: [contributor(), developers(), previous()], 
            replyTo: 'cs@datapandas.com', 
            subject: 'We are building the full pipeline', 
            to: 'cs@datapandas.com'
       } 

}
