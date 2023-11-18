pipeline{
    agent any
    // tools {
    //     maven "MAVEN"
    //     jdk "JAVA_HOME"
    // }
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
        SONARSCCANER = "scanrr"



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
          
		  // environment {
    //          scannerHome = tool 'sonarscanner4'
    //       }

          steps {
            withSonarQubeEnv('scanrr') {
               sh '''${SONARSCCANER}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
             }
           }
         }
    }
}
