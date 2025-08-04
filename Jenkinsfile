pipeline {
    agent any
    tools {
        maven "maven3910"
    }
 /* key stuff 'stages' */
    stages{
        stage("01. GetCode"){ 
          steps{
            git branch: 'main', url: 'https://github.com/benadaba/maven-web-application.git'
          }
        }
        stage("02. Clean"){
            steps{
                sh "mvn clean"
            }
        }
        stage("03. validate"){
            steps{
                sh "mvn validate"
                //sh "mvn -B -DskipTests=true -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true -Djacoco.skip=true -Dfindbugs.skip=true -Dmaven.test.failure.ignore=true clean verify"
            }
        }
        stage("04. Test(UnitTest+Functional+RegressionTest"){
            steps{
                sh "mvn test"
            }
        }
        stage("05. Build"){
            steps{
                sh "mvn package"
                //sh "mvn -B -DskipTests=true -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true -Djacoco.skip=true -Dfindbugs.skip=true -Dmaven.test.failure.ignore=true clean package"
            }
        }
        stage("06. Integration test (mvn verify)"){
            steps{
                sh "mvn verify"
                //sh "mvn -B -DskipTests=true -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true -Djacoco.skip=true -Dfindbugs.skip=true -Dmaven.test.failure.ignore=true clean verify"
            }
        }
        stage("07. CodeQualityAnalysis"){
            steps{
                sh "mvn sonar:sonar"
                //sh "mvn sonar:sonar -Dsonar.projectKey=maven-web-application -Dsonar.host.url=http://localhost:9000 -Dsonar.login=admin -Dsonar.password=admin"
            }
        }
        stage("08. UploadArtifacts"){
            steps{
                sh "mvn deploy"
            }
        }
        stage("09. DeployToApplicationServer-DEV"){
            steps{
                deploy adapters: [tomcat8(alternativeDeploymentContext: '', credentialsId: 'tomcat-credentials', path: '', url: 'http://18.133.223.231:8080/')], contextPath: null, war: 'target/*war'
            }
        }
        stage("10. DeployToApplicationServer-STAGING"){
            steps{
                deploy adapters: [tomcat8(alternativeDeploymentContext: '', credentialsId: 'tomcat-credentials', path: '', url: 'http://18.133.223.231:8080/')], contextPath: null, war: 'target/*war'
            }
        }
        stage("11. ApprovalGate"){
            steps{
                timeout(time: 30, unit: 'SECONDS') {
                    input message: "Do you want to proceed with the deployment to Production?", ok: "Yes, proceed"
                }
            }
        }
         stage("12. DeployToApplicationServer-PROD"){
            steps{
                deploy adapters: [tomcat8(alternativeDeploymentContext: '', credentialsId: 'tomcat-credentials', path: '', url: 'http://18.133.223.231:8080/')], contextPath: null, war: 'target/*war'
            }
        }
        stage("13. Notifications to stakeholders"){
            steps{
                emailext body: '''We are building the full enterprise project for barclays bank plc. This the production pipeline. Please check the progress of this pipeline in this email.

                Thank
                DataPandas DevOps Consultant''', recipientProviders: [buildUser(), culprits(), developers(), brokenBuildSuspects()], subject: 'Please check progress of the build', to: 'cs@datapandas.com' 
            }
        }

    }
    // options{}
    post{
        //no matter what happens in the above stages, always run the code inside the 'always' block
       always{
        sh "echo this will always run no matter what happens in the above stages"
       }
       // the success block will ONLY run if all the stages above are successfu
       success{
          sh "echo JOB was SUCCESSFULL!!!"
       }
       failure{
        sh "echo JOB FAILED!!!"
       } 
    }
}
