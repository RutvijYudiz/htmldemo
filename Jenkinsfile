pipeline {
    agent any

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

