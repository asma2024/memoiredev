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

    stage('Run Backend Tests') {
      steps {
        sh 'npm test'
      }
    }

    stage('Build Backend Docker Image') {
      steps {
        script {
          sh "docker build -t ${backendImageName}:${backendImageTag} ."
        }
      }
    }

    stage('Push Backend Docker Image') {
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

    stage('Docker Remove Backend Image') {
      steps {
        sh "docker rmi ${backendImageName}:${backendImageTag}"
      }
    }

    stage('Build Frontend Docker Image') {
      steps {
        dir('client') {
          script {
            sh "docker build -t ${frontendImageName}:${frontendImageTag} ."
          }
        }
      }
    }

    stage('Push Frontend Docker Image') {
      steps {
        script {
          withCredentials([usernamePassword(
            credentialsId: 'dockerhub',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
          )]) {
            sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
            sh "docker push ${frontendImageName}:${frontendImageTag}"
          }
        }
      }
    }

    stage('Docker Remove Frontend Image') {
      steps {
        sh "docker rmi ${frontendImageName}:${frontendImageTag}"
      }
    }

    stage('Deploying App to Kubernetes') {
      steps {
        sh "kubectl apply -f mongodeploy.yaml"

        sh "sed -i 's|__IMAGE_NAME__|${backendImageName}|g; s|__IMAGE_TAG__|${backendImageTag}|g' backenddeploy.yaml"
        sh "kubectl apply -f backenddeploy.yaml"

        dir('client') {
            sh "sed -i 's|__IMAGE_NAME__|${frontendImageName}|g; s|__IMAGE_TAG__|${frontendImageTag}|g' frontdeploy.yaml"
            sh "kubectl apply -f frontdeploy.yaml"
          }
        }
      }
    }


  post {
    always {
      script {
        def pipelineStatus = currentBuild.result
        def emailBody = pipelineStatus == 'SUCCESS' ? "La pipeline CI/CD a été exécutée avec succès." : "La pipeline CI/CD a été exécutée en échec."
        emailext subject: "Rapport d'exécution de la pipeline CI/CD",
                  body: emailBody,
                  to: "asmahamlia22@gmail.com",
                  attachLog: true
      }
    }
  }
}