pipeline {
    agent any
    
    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
        ECR_REGISTRY = '851725603941.dkr.ecr.ap-south-1.amazonaws.com'
        IMAGE_NAME = 'htmllatestpage'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        KUBE_CONFIG = "/var/lib/jenkins/.kube/config"
        AWS_CREDENTIALS_ID = 'aws-ecr-credentials'
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
                    def dockerImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")

                    // Tag the image for ECR
                    def ecrImage = "${ECR_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}"
                    dockerImage.tag(ecrImage)

                    // Authenticate Docker client to ECR
                    withCredentials([
                        [
                            $class: 'AmazonWebServicesCredentialsBinding',
                            credentialsId: 'aws-ecr-credentials',
                            accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                            secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                        ]
                    ]) {
                        // Login to ECR
                        sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}"

                        // Push the Docker image to ECR
                        sh "docker push ${ecrImage}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying to Kubernetes...'
                script {
                    // Load kubeconfig from Jenkins credentials
                    withCredentials([file(credentialsId: 'kubeconfig-creds', variable: 'KUBECONFIG')]) {
                        // Set KUBECONFIG environment variable
                        sh "export KUBECONFIG=\$KUBECONFIG"
                        
                        // Apply Kubernetes deployment and service manifests
                        sh "kubectl apply -f path/to/htmllatestpagedeployment.yaml"
                        sh "kubectl apply -f path/to/htmllatestpageservice.yaml"
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

