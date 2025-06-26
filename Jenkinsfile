pipeline {
  agent any

  environment {
    IMAGE_NAME = 'teja4340/website'
    K8S_DEPLOYMENT = 'website-deployment'
    K8S_NAMESPACE = 'default'
  }

  stages {
    stage('Clone Repository') {
      steps {
        echo "📥 Cloning the GitHub repository..."
        git 'https://github.com/Tejabaim/website-cicd.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        echo "🐳 Building Docker image: ${IMAGE_NAME}:${BUILD_NUMBER}"
        script {
          docker.build("${IMAGE_NAME}:${BUILD_NUMBER}")
        }
      }
    }

    stage('Push to Docker Hub') {
      steps {
        echo "🔐 Logging in and pushing to Docker Hub..."
        withDockerRegistry([ credentialsId: 'docker-hub-creds', url: '' ]) {
          script {
            docker.image("${IMAGE_NAME}:${BUILD_NUMBER}").push()
            docker.image("${IMAGE_NAME}:${BUILD_NUMBER}").tag('latest')
            docker.image("${IMAGE_NAME}:latest").push()
          }
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        echo "🚀 Deploying to Kubernetes..."
        sh """
        kubectl config use-context your-k8s-context  # optional, if you use multiple contexts
        kubectl set image deployment/${K8S_DEPLOYMENT} \
          nginx=${IMAGE_NAME}:${BUILD_NUMBER} \
          --namespace=${K8S_NAMESPACE} --record
        """
      }
    }
  }

  post {
    success {
      echo "✅ CI/CD pipeline executed successfully!"
    }
    failure {
      echo "❌ Pipeline failed. Check the error logs above."
    }
  }
}
