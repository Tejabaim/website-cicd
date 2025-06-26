pipeline {
  agent any

  environment {
    IMAGE_NAME = 'yourdockerhubusername/website'
    K8S_DEPLOYMENT = 'website-deployment'
  }

  stages {
    stage('Clone Repo') {
      steps {
        git 'https://github.com/yourusername/website-cicd.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          docker.build("${IMAGE_NAME}:${BUILD_NUMBER}")
        }
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withDockerRegistry([ credentialsId: 'docker-hub-creds', url: '' ]) {
          script {
            docker.image("${IMAGE_NAME}:${BUILD_NUMBER}").push()
          }
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh """
        kubectl set image deployment/${K8S_DEPLOYMENT} nginx=${IMAGE_NAME}:${BUILD_NUMBER} --record
        """
      }
    }
  }
}
