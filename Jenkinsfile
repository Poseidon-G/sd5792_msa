pipeline {
    agent any

    environment {
        AWS_REGION = 'us-west-2'
        AWS_ACCOUNT_ID = '800968498659'
        FE_REPO = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/devops-fe"
        BE_REPO = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/devops-be"
        AWS_CREDENTIALS = credentials('aws-credentials')
    }

    stages {
    
        stage('Checkout latest code from scm'){
          steps {
            checkout scm
          }
        }

        stage('Login to ECR') {
            steps {
                script {
                    // Login to Amazon ECR
                    sh """
                    aws configure set default.region ${AWS_REGION}
                    aws configure set aws_access_key_id ${AWS_CREDENTIALS_USR}
                    aws configure set aws_secret_access_key ${AWS_CREDENTIALS_PSW}
                    aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                    """
                }
            }
        }

        stage('Build FE Docker Image and Push to ECR') {
            steps {
                script {
                    // Build FE Docker image
                    sh """
                    echo "Building FE Docker Image"
                    docker build -t fe-image ./src/application/frontend
                    docker tag fe-image:latest ${FE_REPO}:latest
                    """
                }
                script {
                    // Push FE image to ECR
                    echo "Pushing FE Docker Image to ECR"
                    sh "docker push ${FE_REPO}:latest"
                }
            }
        }

        stage('Build BE Docker Image and Push to ECR') {
            steps {
                script {
                    // Build BE Docker image
                    sh """
                    echo "Building BE Docker Image"
                    docker build -t be-image ./src/application/backend
                    docker tag be-image:latest ${BE_REPO}:latest
                    """
                }
                script {
                    // Push BE image to ECR
                    echo "Pushing BE Docker Image to ECR"
                    sh "docker push ${BE_REPO}:latest"
                }
            }
        }
    }

    post {
      always {
        // Cleanup
        sh "docker rmi fe-image be-image"
      }

      success {
        echo 'Pipeline devops for dev executed successfully!!'
      }

      failure {
        echo 'Pipeline devops for dev failed!!'
      }
    }
  }

