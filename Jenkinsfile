pipeline {
  agent any

  environment {
    KUBECONFIG = '/home/jenkins/.kube/config'
  }

  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/shankar-240698/eks-deploy.git'
      }
    }
    stage('Deploy to EKS') {
      steps {
        sh 'ansible-playbook -i inventory.yml deploy.yml'
      }
    }
  }
}
