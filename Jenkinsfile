pipeline {
  agent any

  environment {
    IMAGE_NAME = "halagul/ci-cd-pipeline-demo"
    CONTAINER_NAME = "ci_cd_demo_app"
    GIT_CRED = credentials('GITHUB_TOKEN')
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Install Dependencies') {
      steps {
        sh '''
        docker run --rm -v $(pwd):/app -w /app python:3.11-slim bash -c "pip install -r requirements.txt"
        '''
      }
    }

    stage('Run Tests') {
      steps {
        sh '''
        docker run --rm -v $(pwd):/app -w /app python:3.11-slim bash -c "pip install -r requirements.txt && pytest -q"
        '''
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t ${IMAGE_NAME}:latest .'
      }
    }

    stage('Deploy') {
      steps {
        sh '''
        docker rm -f ${CONTAINER_NAME} || true
        docker run -d --name ${CONTAINER_NAME} -p 5000:5000 ${IMAGE_NAME}:latest
        sleep 4
        curl --fail --silent --show-error http://localhost:5000/ || (docker logs ${CONTAINER_NAME} && exit 1)
        '''
      }
    }
  }

  post {
    success { echo "Pipeline succeeded: ${IMAGE_NAME}:latest" }
    failure { echo "Pipeline failed - check console output" }
  }
}
