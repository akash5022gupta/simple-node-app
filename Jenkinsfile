pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "akash5022gupta/simple-node-app:v1"  // Change this to your Docker image name
        AWS_REGION = 'us-east-1'  // Change this to your AWS region
        EKS_CLUSTER_NAME = 'simple-node-eks-cluster'  // Change this to your EKS cluster name
        KUBERNETES_NAMESPACE = 'default'  // Change if you're using a different Kubernetes namespace
        DOCKER_USER = credentials('akash5022gupta')  // Jenkins Docker Hub Username credential
        DOCKER_PASSWORD = credentials('Ravneetkaur@321')  // Jenkins Docker Hub Password credential
    }
    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/akash5022gupta/simple-node-app.git'  // Replace with your GitHub repo URL
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    sh 'docker login -u $DOCKER_USER -p $DOCKER_PASSWORD'
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    sh 'aws eks --region $AWS_REGION update-kubeconfig --name $EKS_CLUSTER_NAME'
                    sh 'kubectl set image deployment/my-app my-app=$DOCKER_IMAGE --namespace=$KUBERNETES_NAMESPACE'
                }
            }
        }
    }
}
