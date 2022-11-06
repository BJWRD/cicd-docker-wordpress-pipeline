pipeline {
  agent any

  environment {
    DOCKER_HUB_REPO = "bjwrd/docker-wordpress"
    CONTAINER_NAME = "docker-wordpress"
  }
  
  stages {
    stage('Checkout') {
           steps {
               checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/BJWRD/cicd-docker-wordpress-pipeline']]])
            }
    }

    
    stage('Build') {
      steps {
        script {
         echo 'Building Image...'
         sh 'docker image build -t $DOCKER_HUB_REPO:latest .'
        }
      }
    }

    stage('Test Container') {
        steps {
            echo 'Testing Container...'
            sh 'docker stop $CONTAINER_NAME || true'
            sh 'docker rm $CONTAINER_NAME || true'
            sh 'docker run -d --name $CONTAINER_NAME $DOCKER_HUB_REPO'
            sh 'docker stop $CONTAINER_NAME || true'
            sh 'docker rm $CONTAINER_NAME || true'
            sh 'docker system prune'
        }
    }

    stage('Push') {
      steps {
        script {
          echo 'Pushing Image...'
          withDockerRegistry([ credentialsId: "dockerhublogin", url: "" ]) {
          sh 'docker push $DOCKER_HUB_REPO:latest'
          }
        }
      }
    }

       stage('Test Image') {
      steps {
        script {
          echo 'Testing Image...'
          withDockerRegistry([ credentialsId: "dockerhublogin", url: "" ]) {
          sh 'docker scan $DOCKER_HUB_REPO:latest'
          }
        }
      }
    }


    stage('Deploy') {
      steps {
        script {
          echo 'Testing Image...'
          withDockerRegistry([ credentialsId: "dockerhublogin", url: "" ]) {
          sh 'docker pull $DOCKER_HUB_REPO:latest'
          sh 'docker run -d bjwrd/docker-wordpress'
          }
        }
      }
    }
  }
}