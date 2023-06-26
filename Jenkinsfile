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
}


     stage('Build Docker Image') {
        steps {
            script{   
        sh 'cd project-c42 && docker build -t project-c42:${BUILD_NUMBER} . '
        sh 'cd project-c42 && docker tag project-c42:latest 303150498045.dkr.ecr.us-east-1.amazonaws.com/project-c42:${BUILD_NUMBER}'
        sh 'sudo docker push 303150498045.dkr.ecr.us-east-1.amazonaws.com/project-c42:${BUILD_NUMBER}'
            }
      }
    }


    stage('Deploy the application') {
      steps {
        script {
          sh 'cd project-c42 && sudo docker run -d -p 8080:8081 303150498045.dkr.ecr.us-east-1.amazonaws.com/project-c42:${BUILD_NUMBER} '

        }

      }
    }
  }
  post {
    always {
      deleteDir()
      sh 'sudo docker rmi 635145294553.dkr.ecr.us-east-1.amazonaws.com/vote:${BUILD_NUMBER}'
      sh 'sudo docker rmi project-c42:${BUILD_NUMBER}'

    }

  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '10'))
    disableConcurrentBuilds()
    timeout(time: 1, unit: 'HOURS')
  }
}
