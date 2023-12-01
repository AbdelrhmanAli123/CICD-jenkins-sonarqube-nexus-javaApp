# Build-CICD-pipline-using-jenkins-docker-sonarqube-nexus-ECR-ECS

# Technologies 
- Jenkins
- Docker
- maven
- Sonarqube
- Nexus repository 
- Webhook
- aws ecr
- aws ecs
- aws cli
- Slack for notifications

## pipeline architecture design

![CICD design](https://github.com/AbdelrhmanAli123/CI-jenkins-sonarqube-nexus-javaApp/assets/133269614/4626374b-2090-40ba-88c3-0fae058e8818)



# Project steps
#


## Create  3 VMs
- The first for Jenkins server
- The second for the Sonarqube server
- The third for the Nexus server
- use the USER DATA option to configure the servers and install the services during the boot process
- I provided userData script for each server in this repo, check it out

## Jenkins plugins used
- first choose the option recommended plugin installation for basic plugins
- install the Sonarqube scanner plugin
- Install Slack notification plugin
- install Maven integration plugin
- install github intgration
- Install Nexus artifact uploader

## servers configurations 
- Configure Jenkins server
  - create a pipeline add your git credentials and enable the webhook to trigger your pipeline automatically
  - add a credential for Sonarqube as well to allow Sonarqube to scan the code give you the analysis
  - add a credential for Slack notification on the Jenkins server via the Slack notification plugin that we installed before

- configure the nexus server to get three repository 
  - repo to be such as a proxy to install the maven dependencies and third-party modules
  - repo for saving the artifact
  - repo to group these two repos together

- configure sonarqube server 
  - you don't have many things to do in the Sonarqube server but you can create a Sonarqube gate to determine the bugs and vulnerability code limitations

- configure AWS CLI, ECR and ECS 
  - add a role to your Jenkins instance that allows it to control on ECR and ECS
  - create your ECR repo and ECS service and task manually and then you can use the aws cli command provided in the Jenkins pipeline

## Finally run your pipeline it will work in Sha Allah

## Look it works fine! after 61 tries hahaha

![cicd pipline](https://github.com/AbdelrhmanAli123/CI-jenkins-sonarqube-nexus-javaApp/assets/133269614/99d041ed-6376-4199-8c60-34f199d8c695)

