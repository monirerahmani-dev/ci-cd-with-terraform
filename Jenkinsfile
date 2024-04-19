#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@main', retriever: modernSCM(
        [$class: 'GitSCMSource',
        remote: 'https://github.com/monirerahmani-dev/jenkins-shared-library.git',
        credentialsId: 'github-credentials'
  ]
)

pipeline {   
  agent any
  tools {
    maven 'Maven'
  }
  environment {
    IMAGE_NAME = 'monirerahmani/tech-app:jma-4.0'
  }
  stages {
    stage("build app") {
      steps {
        script {
          echo 'building application jar...'
          buildJar()
        }
      }
    }
    stage("build image") {
      steps {
        script {
          echo 'building docker image...'
          buildImage(env.IMAGE_NAME)
          dockerLogin()
          dockerPush(env.IMAGE_NAME)
        }
      }
    }
    stage("deploy") {
      steps {
        script {
          echo 'deploying docker image to EC2...'
          
          def shellCmd = "bash ./server-cmds.sh ${IMAGE_NAME}"
          def ec2Instance = "ec2-user@$35.180.151.121"

          sshagent(['server-ssh-key']) {
            sh "scp -o server-cmds.sh ${ec2Instance}:/home/ec2-user"
            sh "scp -o docker-compose.yaml ${ec2Instance}:/home/ec2-user"
            sh "ssh -o StrictHostKeyChecking=no ${ec2Instance} ${shellCmd}"
          }
        }
      }
    }               
  }
}

