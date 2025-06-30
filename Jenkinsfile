pipeline {
    agent any

    environment {
        AWS_REGION = 'eu-north-1'
        ECR_REGISTRY = '339388639916.dkr.ecr.eu-north-1.amazonaws.com'
        IMAGE_NAME = 'flask-app'
        CLUSTER_NAME = 'mycluster'
        DEPLOYMENT_NAME = 'flask-deployment'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/malleswari23/flask-application.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REGISTRY'
                    sh 'docker build -t $IMAGE_NAME .'
                    sh 'docker tag $IMAGE_NAME:latest $ECR_REGISTRY/$IMAGE_NAME:latest'
                }
            }
        }

        stage('Push to ECR') {
            steps {
                sh 'docker push $ECR_REGISTRY/$IMAGE_NAME:latest'
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    // update image in the deployment.yaml
                    sh '''
                    sed -i "s|image:.*|image: $ECR_REGISTRY/$IMAGE_NAME:latest|" deployment.yaml
                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml
                    '''
                }
            }
        }
    }
}
