pipeline {
    agent any
    
    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
        ECR_REGISTRY = '851725603941.dkr.ecr.ap-south-1.amazonaws.com'
        IMAGE_TAG = "${BUILD_NUMBER}"
        KUBE_CONFIG = "/var/lib/jenkins/.kube/config"
        AWS_CREDENTIALS_ID = 'aws-ecr-credentials'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                script {
                    docker.build("htmllatestpage:${IMAGE_TAG}")
                    docker.withRegistry(ECR_REGISTRY, 'ecr:your-ecr-credentials-id') {
                        docker.image("htmllatestpage:${IMAGE_TAG}").push()
                    }
                }
            }
        }
        
         stage('Push to ECR') {
            steps {
                echo 'Pushing Docker image to ECR...'
                script {
                    def dockerImage = docker.image("htmllatestpage:${IMAGE_TAG}")
                    docker.withRegistry(ECR_REGISTRY, 'ecr:your-ecr-credentials-id') {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying to Kubernetes...'
                script {
                    withCredentials([file(credentialsId: 'kubeconfig-creds', variable: 'KUBECONFIG')]) {
                        sh """
                        export KUBECONFIG=\$KUBECONFIG
                        kubectl apply -f path/to/htmllatestpagedeployment.yaml
                        kubectl apply -f path/to/htmllatestpageservice.yaml
                        """
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


