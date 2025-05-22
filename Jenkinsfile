pipeline {
agent any

environment {
AWS_DEFAULT_REGION = 'us-east-1'
S3_BUCKET = 's3htmlpage'
IMAGE_NAME = 'msshankar/htmlpage'
}

stages {
stage('Checkout') {
steps {
git branch: 'main', url: 'https://github.com/shankar-240698/htmlpage.git'
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

stage('Upload to S3') {
  steps {
    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials']]) {
      sh '''
        aws s3 cp build.zip s3://$S3_BUCKET/jenkins-artifacts/${GIT_COMMIT}.zip
      '''
    }
  }
}

stage('Build & Push Docker Image') {
  steps {
    script {
      def tag = GIT_COMMIT.take(7)
      def fullTag = "${IMAGE_NAME}:${tag}"
      env.IMAGE_TAG = fullTag

      sh "docker build -t ${fullTag} ."
      withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
        sh '''
          echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
          docker push ${IMAGE_TAG}
        '''
      }
    }
  }
}

stage('Deploy with Ansible') {
  steps {
    sshagent(credentials: ['ssh-key']) {
      sh '''
        ansible-playbook -i ansible/inventory.yml ansible/deploy.yml \
          -e "docker_image=${IMAGE_TAG}" \
          --private-key ~/.ssh/id_rsa
      '''
    }
  }
}
}
}
