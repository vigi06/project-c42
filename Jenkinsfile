pipeline {
  agent {
    label 'worker'
  }
  stages {
    stage('Git Checkout') {
      steps {
        checkout([$class: 'GitSCM',
                  branches: [[name: '*/main']],
                  userRemoteConfigs: [[url: 'https://github.com/vigi06/project-c42.git']]])
      }
    }

    stage('Build Docker Image') {
      steps {
        script {  
          sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 303150498045.dkr.ecr.us-east-1.amazonaws.com'   
          sh 'sudo docker build -t project-c42:${BUILD_NUMBER} . '
          sh 'sudo docker tag project-c42:${BUILD_NUMBER} 303150498045.dkr.ecr.us-east-1.amazonaws.com/project-c42:${BUILD_NUMBER}'
          sh 'sudo docker push 303150498045.dkr.ecr.us-east-1.amazonaws.com/project-c42:${BUILD_NUMBER}'
        }
      }
    }

    stage('Cleanup the docker image') {
      steps {
        script {
          sh 'sudo docker rmi 303150498045.dkr.ecr.us-east-1.amazonaws.com/project-c42:${BUILD_NUMBER}'
          sh 'sudo docker rmi project-c42:${BUILD_NUMBER}'
        }
      }
    }

    stage('Deploy the application') {
      steps {
        script {
          sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 303150498045.dkr.ecr.us-east-1.amazonaws.com'  
          sh 'sudo docker pull 303150498045.dkr.ecr.us-east-1.amazonaws.com/project-c42:${BUILD_NUMBER}'  
          sh 'sudo docker run -d -p 8080:8081 303150498045.dkr.ecr.us-east-1.amazonaws.com/project-c42:${BUILD_NUMBER} '
        }
      }
    }
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '10'))
    disableConcurrentBuilds()
    timeout(time: 1, unit: 'HOURS')
  }
}
