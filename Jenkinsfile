pipeline {
  agent any
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')  // Jenkins credentials id
    DOCKER_IMAGE_NAME = "msshankar/htmlpage"
    KUBECONFIG = '/home/jenkins/.kube/config'
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', 'https://github.com/shankar-240698/eks-deploy.git'
      }
    }
  }
    stage('Build Docker Image') {
      steps {
        script {
          dockerImage = docker.build("${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}")
        }
      }
    }

    stage('Push to Docker Hub') {
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-creds') {
            dockerImage.push()
          }
        }
      }
    }

    stage('Deploy with Ansible') {
      steps {
        sh 'ansible-playbook -i inventory.yml deploy.yml'
      }
    }
  }
}
