pipeline {
  agent {
    label 'Jenkins-Agent'
  }
  tools {
    jdk 'java17'
    maven 'Maven3'
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
  }
}
