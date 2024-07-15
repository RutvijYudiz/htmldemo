pipeline {
    agent any
<<<<<<< HEAD
    
    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
        ECR_REGISTRY = '851725603941.dkr.ecr.ap-south-1.amazonaws.com'
        IMAGE_TAG = "${BUILD_NUMBER}"
        KUBE_CONFIG = credentials('kubeconfig-creds')
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
=======

    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
        ECR_REGISTRY = '851725603941.dkr.ecr.ap-south-1.amazonaws.com'
        IMAGE_TAG = sh(returnStdout: true, script: 'echo $BUILD_NUMBER').trim()
        KUBE_CONFIG = "/var/lib/jenkins/.kube/config"
        AWS_CREDENTIALS_ID='aws-ecr-credentials'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                git branch: 'main', url: 'https://github.com/RutvijYudiz/htmldemo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                script {
                    docker.build("htmllatestpage:${IMAGE_TAG}")
                }
                echo 'Building Docker image...Done'
            }
        }

        stage('Push to ECR') {
            steps {
                echo 'Pushing Docker image to ECR...'
                script {
                    docker.withRegistry("https://${ECR_REGISTRY}", "${AWS_CREDENTIALS_ID}") {
                        docker.image("htmllatestpage:${IMAGE_TAG}").push()
                    }
                }
                echo 'Pushing Docker image to ECR...Done'
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
                        kubectl apply -
>>>>>>> 9df5132673592ae207f3bcacc5221f9e4fe079f5

