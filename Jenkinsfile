pipeline {
  agent any

  environment {
    AWS_REGION = 'us-east-1'
    ECR_REPO_NAME = 'my-flask-app'
    AWS_ACCOUNT_ID = '9770-9898-5978'
    IMAGE_TAG = 'latest'
    ECR_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO_NAME}:${IMAGE_TAG}"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t ${ECR_REPO_NAME}:${IMAGE_TAG} .'
      }
    }

    stage('Tag Docker Image for ECR') {
      steps {
        sh 'docker tag ${ECR_REPO_NAME}:${IMAGE_TAG} ${ECR_URI}'
      }
    }

    stage('Authenticate to ECR') {
      steps {
        sh 'aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com'
      }
    }

    stage('Push to ECR') {
      steps {
        sh 'docker push ${ECR_URI}'
      }
    }
  }
}
