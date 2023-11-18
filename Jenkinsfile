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
        stage('SonarQube analysis'){
            steps{ 
                script{

                withSonarQubeEnv(credentialsId: 'sonarqube'){
                    sh "mvn sonar:sonar"
                }
              }

            }
        }
    }
}