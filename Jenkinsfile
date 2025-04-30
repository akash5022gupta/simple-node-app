pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "akash5022gupta/simple-node-app:v1"
        AWS_REGION = 'us-east-1'
        EKS_CLUSTER_NAME = 'simple-node-eks-cluster'
        KUBERNETES_NAMESPACE = 'default'
        DOCKER_CREDENTIALS = credentials('docker-hub-credentials')  // Docker Hub credentials
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/akash5022gupta/simple-node-app.git'

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
                    sh "docker login -u ${DOCKER_CREDENTIALS_USR} -p ${DOCKER_CREDENTIALS_PSW}"
                    sh "docker push $DOCKER_IMAGE"
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', 
                                      accessKeyVariable: 'AWS_ACCESS_KEY_ID', 
                                      secretKeyVariable: 'AWS_SECRET_ACCESS_KEY', 
                                      credentialsId: 'aws-credentials']]) {
                        // Securely access AWS credentials
                        sh 'aws eks --region $AWS_REGION update-kubeconfig --name $EKS_CLUSTER_NAME'
                        sh 'kubectl set image deployment/my-app my-app=$DOCKER_IMAGE --namespace=$KUBERNETES_NAMESPACE'
                    }
                }
            }
        }
    }
}
