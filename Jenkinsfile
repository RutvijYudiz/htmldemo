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
        
        stage('Build') {
            steps {
                script {
                    docker.build("${ECR_REGISTRY}/htmllatestpage:${IMAGE_TAG}", 'docker/')
                }
            }
      
           
        }
        
        stage('Push to ECR') {
            steps {
                script {
                    docker.withRegistry("https://${ECR_REGISTRY}", "${AWS_CREDENTIALS_ID}") {
                        docker.image("${ECR_REGISTRY}/htmllatestpage:${IMAGE_TAG}").push()
                    }
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl --kubeconfig=\$KUBE_CONFIG apply -f htmllatestpagedeployment.yaml"
                    sh "kubectl --kubeconfig=\$KUBE_CONFIG apply -f htmllatestpageservice.yaml"
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}

