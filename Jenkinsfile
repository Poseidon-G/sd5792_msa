pipeline {
    agent any

    environment {
        AWS_REGION = 'eu-west-2'
        AWS_ACCOUNT_ID = '800968498659'
        FE_REPO = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/devops-fe"
        BE_REPO = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/devops-be"
    }

    stages {
        stage('Checkout latest code from scm') {
            steps {
                echo "Checking out latest code..."
                checkout scm
            }
        }

        stage('Build FE Docker Image and Push to ECR') {
            steps {
                script {
                    echo "Logging in to Amazon ECR..."
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials']]) {
                          sh """
                          aws configure set default.region '${AWS_REGION}'
                          aws ecr get-login-password --region '${AWS_REGION}' | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                          """
                      }
                }
                script {
                    echo "Building FE Docker Image..."
                    sh """
                    docker build -t fe-image ./src/application/frontend
                    docker tag fe-image:latest ${FE_REPO}:latest
                    """
                }
                script {
                    echo "Pushing FE Docker Image to ECR..."
                    sh "docker push ${FE_REPO}:latest"
                }
            }
        }

        stage('Build BE Docker Image and Push to ECR') {
            steps {
                script {
                      echo "Logging in to Amazon ECR..."
                      withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials']]) {
                            sh """
                            aws configure set default.region '${AWS_REGION}'
                            aws ecr get-login-password --region '${AWS_REGION}' | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                            """
                        }
                  }
                script {
                    echo "Building BE Docker Image..."
                    sh """
                    docker build -t be-image ./src/application/backend
                    docker tag be-image:latest ${BE_REPO}:latest
                    """
                }
                script {
                    echo "Pushing BE Docker Image to ECR..."
                    sh "docker push ${BE_REPO}:latest"
                }
            }
        }
    }

    post {

        success {
            echo 'Pipeline devops for dev executed successfully!!'
        }

        failure {
            echo 'Pipeline devops for dev failed!!'
        }
    }
}
