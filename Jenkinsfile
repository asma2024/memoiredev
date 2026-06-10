pipeline {
  environment {
    backendImageName = "asmaha22/backend"
    backendImageTag = "${BUILD_NUMBER}"
    frontendImageName = "asmaha22/frontend"
    frontendImageTag = "${BUILD_NUMBER}"
  }

  agent any

  stages {

    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/asma2024/memoiredev'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'rm -rf node_modules'
        sh 'npm install --prefer-offline --no-audit --no-fund'
      }
    }

    stage('Docker Build') {
      steps {
        script {
          sh "docker build -t ${backendImageName}:${backendImageTag} ."
        }
      }
    }

    stage('Push Docker Hub') {
      steps {
        script {
          withCredentials([usernamePassword(
            credentialsId: 'dockerhub',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
          )]) {
            sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
            sh "docker push ${backendImageName}:${backendImageTag}"
          }
        }
      }
    }

    stage('Cleanup') {
      steps {
        sh "docker rmi ${backendImageName}:${backendImageTag}"
      }
    }

  }

  post {
    success {
      echo "Build réussi !"
    }
    failure {
      echo "Build échoué. Vérifiez les logs."
    }
  }
}
