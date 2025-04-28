pipeline {
    agent any

    environment {
        GITHUB_CREDENTIALS = credentials('github-token')  // GitHub token for repo access
        DOCKER_IMAGE = 'akash5022gupta/simple-node-app'   // Docker image name
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/akash5022gupta/simple-node-app.git',
                        credentialsId: 'github-token'
                    ]]])
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    try {
                        sh "docker build -t ${DOCKER_IMAGE}:latest ."
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }

        stage('Login and Push Docker Image') {
            steps {
                script {
                    try {
                        docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                            sh "docker push ${DOCKER_IMAGE}:latest"
                        }
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    try {
                        sh 'kubectl apply -f k8s/deployment.yaml'
                        sh 'kubectl apply -f k8s/service.yaml'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for errors.'
        }
    }
}
environment {
        AWS_CREDENTIALS = credentials('aws-credentials')  // Reference to your AWS credentials in Jenkins
    }

    stages {
        stage('Deploy to EKS') {
            steps {
                script {
                    // Configure AWS CLI with credentials
                    sh 'aws configure set aws_access_key_id ${AWS_CREDENTIALS_USR}'
                    sh 'aws configure set aws_secret_access_key ${AWS_CREDENTIALS_PSW}'
                    sh 'aws configure set region us-west-2'  // Set your desired AWS region

                    // Update kubeconfig for EKS
                    sh 'aws eks --region us-west-2 update-kubeconfig --name <cluster-name>'

                    // Apply the Kubernetes deployment and service
                    sh 'kubectl apply -f k8s/deployment.yaml'
                    sh 'kubectl apply -f k8s/service.yaml'
                }
            }
        }
    }

