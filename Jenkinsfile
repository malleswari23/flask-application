pipeline {
    agent any

    environment {
        AWS_REGION = "eu-north-1"
        ECR_REPO_URI = "339388639916.dkr.ecr.eu-north-1.amazonaws.com/flask-app"
        IMAGE_TAG = "latest"
        CLUSTER_NAME = "mycluster"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/malleswari23/flask-application.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t flask-app:${IMAGE_TAG} ."
                    sh "docker tag flask-app:${IMAGE_TAG} ${ECR_REPO_URI}:${IMAGE_TAG}"
                }
            }
        }

        stage('Login to ECR') {
            steps {
                script {
                    sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REPO_URI}"
                }
            }
        }

        stage('Push Image to ECR') {
            steps {
                script {
                    sh "docker push ${ECR_REPO_URI}:${IMAGE_TAG}"
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    sh "aws eks --region ${AWS_REGION} update-kubeconfig --name ${CLUSTER_NAME}"
                    sh "kubectl apply -f deployment.yaml"
                    sh "kubectl apply -f service.yaml"
                }
            }
        }
    }
}

