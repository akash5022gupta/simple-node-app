pipeline {
    agent any

    environment {
        GITHUB_CREDENTIALS = credentials('github-token') // Make sure ID matches your credentials ID in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']], // IMPORTANT: using main branch
                    userRemoteConfigs: [[
                        url: 'https://github.com/akash5022gupta/simple-node-app.git',
                        credentialsId: 'github-token'
                    ]]
                ])
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t akash5022gupta/simple-node-app:latest .'
                    sh 'docker push akash5022gupta/simple-node-app:latest'
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    sh 'kubectl apply -f k8s/deployment.yaml'
                    sh 'kubectl apply -f k8s/service.yaml'
                }
            }
        }
    }
}
