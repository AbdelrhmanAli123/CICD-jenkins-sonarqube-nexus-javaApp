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
 //        SONARSCCANER = "sonarserver"
	// SONARSERVER = "sonarserver"

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
          
		  environment {
             scannerHome = tool 'sonarserver'
          }

          steps {
            withSonarQubeEnv("sonarqube") {
               sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=my-project \
                   -Dsonar.projectName=my-project \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml
		   - Dsonar.host.url=http://3.80.146.132:9000'''
            }

            timeout(time: 10, unit: 'MINUTES') {
               waitForQualityGate abortPipeline: true
            }
          }
        }
    }
}
