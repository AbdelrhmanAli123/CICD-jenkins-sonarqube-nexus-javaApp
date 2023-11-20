def COLOR_MAP=[
	'SUCCESS':'good',
	'FAILURE':'danger'
]
pipeline{
    agent any
    tools {
        maven "maven_home"
        jdk "java_home"
    }
    environment{
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'adminadmin'
        NEXUS_URL = "172.31.51.113:8081"
        NEXUSIP = "172.31.51.113"
        NEXUSPORT="8081"
        NEXUS_REPOSITORY = "AbdelrhmanRepo"
    	NEXUS_REPOGRP_ID    = "vprofile-grp-repo"
        NEXUS_CREDENTIAL_ID = "nexus"
        NEXUS_GRP_REPO = "vpro-maven-group"
        ARTVERSION = "${env.BUILD_ID}"

    }
    stages{
        stage('build_stage'){
            steps{
                sh 'mvn -s settings.xml -DskipTests install'
            }
            post{

                success{

                    echo "archiving now"
                    archiveArtifacts artifacts: '**/*.war' 
                }
                failure{
                    echo "there's a problem in build stage"
                }
            }
        }
        stage('test_stage'){
            steps{
                sh 'mvn -s settings.xml test  '
            }
        }
	stage('CODE ANALYSIS with SONARQUBE') {
		steps{	
			script{
				
		withSonarQubeEnv(credentialsId: 'sonar-tokan') {
				sh 'mvn  -s settings.xml sonar:sonar'
			}
		     } 
			
		}
	}
    //                  ----------Other way to use sonarqube-------------
    //     stage('CODE ANALYSIS with SONARQUBE') {
          
		  // environment {
    //          scannerHome = tool 'sonarserver'
    //       }

    //  //      steps {
    //  //        withSonarQubeEnv("sonarserver") {
    //  //           sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=my-project \
    //  //               -Dsonar.projectName=my-project \
    //  //               -Dsonar.projectVersion=1.0 \
    //  //               -Dsonar.sources=src/ \
    //  //               -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
    //  //               -Dsonar.junit.reportsPath=target/surefire-reports/ \
    //  //               -Dsonar.jacoco.reportsPath=target/jacoco.exec \
    //  //               -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml
		  //  // - Dsonar.host.url=http://3.80.146.132:9000'''
    //  //        }

     //        timeout(time: 10, unit: 'MINUTES') {
     //           waitForQualityGate abortPipeline: true
     //        }
     //      }
     //    }
	    
	stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }   
	stage("Upload Artifact to Nexus"){
            steps{
                nexusArtifactUploader(
                  nexusVersion: 'nexus3',
                  protocol: 'http',
                  nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                  groupId: 'QA',
                  version: "${env.BUILD_ID}",
                  repository: "${NEXUS_REPOSITORY}",
                  credentialsId: "${NEXUS_CREDENTIAL_ID}",
                  artifacts: [
                    [artifactId: 'myArtifact',
                     classifier: '',
                     file: 'target/vprofile-v2.war',
                     type: 'war']
                  ]
                )
            }
        }
	stage("push the imaegs to ecr"){
		 steps{
			 script{

				 sh """
				# Step 1: Authenticate Docker with Amazon ECR in the us-east-1 region
				aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 347735122858.dkr.ecr.us-east-1.amazonaws.com
				
				# Step 2: Build the Docker image for the 'app' service with the specified build number
				docker build -t app:${BUILD_NUMBER} ./app/.
				
				# Step 3: Build the Docker image for the 'web' service with the specified build number
				docker build -t web:${BUILD_NUMBER} ./wep/.
				
				# Step 4: Build the Docker image for the 'db' service with the specified build number
				docker build -t db:${BUILD_NUMBER}  ./db/.
				
				# Step 5: Tag the 'app' Docker image with the ECR repository and build number
				docker tag app:${BUILD_NUMBER} 347735122858.dkr.ecr.us-east-1.amazonaws.com/cicd-repo:app{BUILD_NUMBER}
				
				# Step 6: Tag the 'web' Docker image with the ECR repository and build number
				docker tag web:${BUILD_NUMBER} 347735122858.dkr.ecr.us-east-1.amazonaws.com/cicd-repo:web{BUILD_NUMBER}
				
				# Step 7: Tag the 'db' Docker image with the ECR repository and build number
				docker tag db:${BUILD_NUMBER} 347735122858.dkr.ecr.us-east-1.amazonaws.com/cicd-repo:db{BUILD_NUMBER}
         			# Push the app image
				docker push 347735122858.dkr.ecr.us-east-1.amazonaws.com/cicd-repo:app{BUILD_NUMBER}
				
				# Push the web image
				docker push 347735122858.dkr.ecr.us-east-1.amazonaws.com/cicd-repo:web{BUILD_NUMBER}
				
				# Push the db image
				docker push 347735122858.dkr.ecr.us-east-1.amazonaws.com/cicd-repo:db{BUILD_NUMBER}

			 """
			 }
		 }   
	    }
	    stage("Deploy to ECS") {
		    steps {
		        script {
		            // ECS Cluster and Service configuration
		            def ecsCluster = "your-ecs-cluster-name"
		            def appServiceName = "app-service"  // Update with your ECS service name for the 'app' service
		            def webServiceName = "web-service"  // Update with your ECS service name for the 'web' service
		            def dbServiceName = "db-service"    // Update with your ECS service name for the 'db' service
		
		            // ECS Task Definitions
		            def appTaskDefinition = "app-task"  // Update with your ECS task definition for the 'app' service
		            def webTaskDefinition = "web-task"  // Update with your ECS task definition for the 'web' service
		            def dbTaskDefinition = "db-task"    // Update with your ECS task definition for the 'db' service
		
		            // ECS Service Update for 'app' service
		            sh "aws ecs update-service --cluster ${ecsCluster} --service ${appServiceName} --task-definition ${appTaskDefinition}:${BUILD_NUMBER}"
		
		            // ECS Service Update for 'web' service
		            sh "aws ecs update-service --cluster ${ecsCluster} --service ${webServiceName} --task-definition ${webTaskDefinition}:${BUILD_NUMBER}"
		
		            // ECS Service Update for 'db' service
		            sh "aws ecs update-service --cluster ${ecsCluster} --service ${dbServiceName} --task-definition ${dbTaskDefinition}:${BUILD_NUMBER}"
		        }
		    }
		}
       }
	post {
        always {
            echo 'Slack Notifications.'
            slackSend channel: '#devops-pipline-notification',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}
