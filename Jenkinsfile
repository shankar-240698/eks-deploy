pipeline {
    agent any

    environment {
        KUBECONFIG = credentials('kubeconfig-cred-id')  // Jenkins credentials ID for kubeconfig file
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "main" , 'https://github.com/shankar-240698/eks-deploy.git' // Replace with your Git repo
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                    echo "Deploying Nginx to EKS..."
                    kubectl apply -f nginx-deployment.yaml
                    kubectl apply -f nginx-service.yaml
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl get pods'
                sh 'kubectl get svc nginx-service'
            }
        }
    }
}
