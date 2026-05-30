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
        git 'https://github.com/asma2024/memoiredev'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }
    }
    }
