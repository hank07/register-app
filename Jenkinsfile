pipeline {
  agent {
    label 'Jenkins-Agent'
  }
  tools {
    jdk 'java17'
    maven 'Maven3'
  }
  environment {
    APP_NAME = "register-app-pipeline"
    RELEASE = "1.0.0"
    DOCKER_USER = "hank07"
    DOCKER_PASS = 'dockerhub'
    IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
    IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
  }
  stages {
    stage("CleanUp Workspace") {
      steps {
        cleanWs()
      }
    }
    stage("SCM Checkout") {
      steps {
        git branch: 'main', credentialsId: 'github', url: 'https://github.com/hank07/register-app.git'
      }
    }
    stage("Build Application") {
      steps {
        sh "mvn clean package"
      }
    }
    stage("Test Application") {
      steps {
        sh "mvn test"
      }
    }
    stage("Build and Push Docker") {
      steps {
        script {
          // Build Docker image
          sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."

          // Log in to Docker Hub
          withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
            sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}"
          }

          // Push Docker image to Docker Hub
          sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
        }
      }
    }
  }
}
