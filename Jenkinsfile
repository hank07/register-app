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
    JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
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
    stage ('Cleanup Artifacts') {
           steps {
               script {
                    // sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    // sh "docker rmi ${IMAGE_NAME}:latest"
                    echo "CLeaning Artifacts"
               }
          }
       }
    stage("Trigger CD Pipeline") {
            steps {
                script {
                    sh "curl -v -k --user clouduser:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'ec2-54-174-175-161.compute-1.amazonaws.com:8080/job/gitops-register-app-cd/buildWithParameters?token=gitops-token'"
                }
            }
       }
  }
}
