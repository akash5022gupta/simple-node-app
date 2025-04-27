pipeline {
    agent any

    environment {
        GITHUB_CREDENTIALS = credentials('github-token') // Reference to the GitHub token you added in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'github-token', url: 'https://github.com/akash5022gupta/simple-node-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Replace 'akash5022gupta' with your Docker Hub username
                    sh 'docker build -t akash5022gupta/simple-node-app:latest .'
                    sh 'docker push akash5022gupta/simple-node-app:latest'
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    // Make sure kubectl is installed and configured on Jenkins instance
                    sh 'kubectl apply -f k8s/deployment.yaml'
                    sh 'kubectl apply -f k8s/service.yaml'
                }
            }
        }
    }
}
