pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'  // AWS region
        ECR_REGISTRY = '851725603941.dkr.ecr.ap-south-1.amazonaws.com'  // ECR registry URL
        IMAGE_NAME = 'htmllatestpage'  // Docker image name
        IMAGE_TAG = "${env.BUILD_NUMBER}"  // Docker image tag
        AWS_CREDENTIALS_ID = 'aws-ecr-credentials'  // Jenkins credentials ID for AWS
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                script {
                    // Build Docker image with the specified tag
                    sh "docker build -t ${ECR_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} ."

                    // Authenticate Docker client to ECR
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
                                      credentialsId: AWS_CREDENTIALS_ID,
                                      accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                                      secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                        // Login to ECR
                        sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}"

                        // Push the Docker image to ECR
                        sh "docker push ${ECR_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
                                      credentialsId: AWS_CREDENTIALS_ID,
                                      accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                                      secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                        // Set the image of the existing deployment to the new image
                        sh "kubectl set image deployment/${IMAGE_NAME}-deployment ${IMAGE_NAME}=${ECR_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} --record"
                    }
                }
            }
        }

        stage('Automated Tests') {
            steps {
                echo 'Running automated tests...'
                // Add automated tests here
            }
        }

        stage('Monitor and Notify') {
            steps {
                echo 'Monitoring and notification setup...'
                // Integrate with monitoring and alerting tools
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
            // Notify stakeholders or perform post-deployment actions
        }
        failure {
            echo 'Deployment failed!'
            // Send alerts or rollback actions
        }
    }
}

