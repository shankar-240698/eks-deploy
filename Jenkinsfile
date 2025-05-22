pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shankar-240698/eks-deploy.git'
            }
        }
        stage('Build & Archive Artifacts') {
            steps {
                sh '''
              mkdir -p build
              cp index.html about.html -r css build/
              zip -r build.zip build
            '''
            archiveArtifacts artifacts: 'build.zip', fingerprint: true
  }
}

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t myimage:latest .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                        echo "$PASSWORD" | docker login -u "$USERNAME" --password-stdin
                        docker tag myimage:latest $USERNAME/myimage:latest
                        docker push $USERNAME/myimage:latest
                    '''
                }
            }
        }

        stage('Deploy with Ansible') {
            steps {
                sh 'ansible-playbook -i ansible/inventory.yml ansible/deploy.yml'
            }
        }
    }
}
