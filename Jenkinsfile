pipeline {
  agent any
  environment {
    AWS_REGION = 'us-east-1'
    ECR_REPO = 'my-flask-app'
    AWS_ACCOUNT_ID = '977098985978'
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Build Docker Image') {
      steps {
        sh 'docker build -t ${ECR_REPO}:latest .'
      }
    }
stage('Create ECR Repository if not exists') {
  steps {
    script {
      def exists = sh(
        script: "aws ecr describe-repositories --repository-names ${ECR_REPO} --region ${AWS_REGION}",
        returnStatus: true
      )
      if (exists != 0) {
        echo "ECR repository ${ECR_REPO} does not exist. Creating..."
        sh "aws ecr create-repository --repository-name ${ECR_REPO} --region ${AWS_REGION}"
        echo "Waiting 10 seconds for repo to be ready..."
        sleep 10
      } else {
        echo "ECR repository ${ECR_REPO} already exists."
      }
    }
  }
}
    stage('Tag Docker Image for ECR') {
      steps {
        sh 'docker tag ${ECR_REPO}:latest ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:latest'
      }
    }
    stage('Authenticate to ECR') {
      steps {
        sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
      }
    }
    stage('Push to ECR') {
      steps {
        sh 'docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:latest'
      }
    }
  }
}
