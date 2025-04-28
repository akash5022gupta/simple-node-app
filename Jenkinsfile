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
                    branches: [[name: '*/main']], // Branch reference
                    userRemoteConfigs: [[
                        url: 'https://github.com/akash5022gupta/simple-node-app.git',
                        credentialsId: 'github-token' // Ensure GitHub token credentials are correct
                    ]]
                ])
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t ${DOCKER_IMAGE}:latest .'
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    // Docker login to Docker Hub
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        echo 'Logged in to Docker Hub'
                    }
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    // Push Docker image to Docker Hub
                    sh 'docker push ${DOCKER_IMAGE}:latest'
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    // Deploy to EKS (ensure kubectl is set up and authenticated with EKS)
                    sh 'kubectl apply -f k8s/deployment.yaml'
                    sh 'kubectl apply -f k8s/service.yaml'
                }
            }
        }
    }
}
