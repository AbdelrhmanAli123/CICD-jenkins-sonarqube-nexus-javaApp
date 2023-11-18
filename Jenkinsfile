pipeline{
    agent any
    tools {
        maven "MAVEN"
        jdk "JAVA_HOME"
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
        SONARSERVER = 'my_sonarqube'
        SONARSCANNER = 'my_sonarqube'
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
        // stage('SonarQube analysis using maven'){
        //     steps{
        //         withSonarQubeEnv('my_sonarqube'){ 
                    
        //             sh 'mvn sonar:sonar'
        //         } 
        //     }
        // }

        stage('SonarQube analysis nativly'){
           steps{
            
            withSonarQubeEnv('my_sonarqube') {
               sh '''${SONARSCANNER}/bin/sonar-scanner -Dsonar.projectKey=sonar-project \
                   -Dsonar.projectName=sonar-project \
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