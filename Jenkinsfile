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
		  stage('SonarQube analysis') {
		    def scannerHome = tool 'sonarserver';
		    withSonarQubeEnv('sonarserver') {
		      sh "${scannerHome}/bin/sonar-scanner \
		      -D sonar.login=admin \
		      -D sonar.password=admin \
		      -D sonar.projectKey=sonarqubetest \
		      -D sonar.exclusions=vendor/**,resources/**,**/*.java \
		      -D sonar.host.url="3.80.146.132"
		 
          
    	     }
         }
    }
}
