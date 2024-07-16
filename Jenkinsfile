pipeline {
    agent any
    
    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'  // Set your AWS region
        ECR_REGISTRY = '851725603941.dkr.ecr.ap-south-1.amazonaws.com'  // Set your ECR registry URL
        IMAGE_NAME = 'htmllatestpage'  // Set your Docker image name
        IMAGE_TAG = "${env.BUILD_NUMBER}"  // Set your Docker image tag
        KUBE_CONFIG = "/var/lib/jenkins/.kube/config"  // Path to Kubernetes config
        AWS_CREDENTIALS_ID = 'aws-ecr-credentials'  // Jenkins credentials ID for AWS
        MANIFESTS_PATH = '/var/lib/jenkins/k8s-manifests'
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
                    withEnv(['KUBECONFIG=${KUBE_CONFIG}']) {
                        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
                                          credentialsId: AWS_CREDENTIALS_ID,
                                          accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                                          secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                            
                            // Validate access to Kubernetes
                            sh "kubectl get nodes"
                            
                            // Substitute the image name in the Kubernetes deployment manifest
                            sh "sed -i 's|image: .*|image: ${ECR_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}|g' ${MANIFESTS_PATH}/htmllatestpagedeployment.yaml"

                            // Apply Kubernetes deployment and service manifests
                            sh "kubectl apply -f ${MANIFESTS_PATH}/htmllatestpagedeployment.yaml"
                            sh "kubectl apply -f ${MANIFESTS_PATH}/htmllatestpageservice.yaml"

                            // Describe deployment for debugging
                            sh "kubectl describe deployment htmllatestpage-deployment"
                        }
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

