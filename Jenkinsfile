pipeline {
  agent any

  environment {
    DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials') // Jenkins stored creds ID
    IMAGE_NAME = 'msshankar/htmlpage'
  }

  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/shankar-240698/eks-deploy'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          dockerImage = docker.build("${IMAGE_NAME}:${env.BUILD_NUMBER}")
        }
      }
    }

    stage('Login to Docker Hub') {
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
            dockerImage.push()
            dockerImage.push('latest')
          }
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh 'ansible-playbook -i inventory/hosts deploy.yml'
      }
    }
  }
}
