pipeline {
    agent any
    environment {
        // Define environment variables
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials') // Docker Hub credentials ID
        AZURE_CREDENTIALS = credentials('azure-credentials') // Azure Service Principal credentials ID
        DOCKER_IMAGE = "tiwariadarsh/python_app"
        AKS_CLUSTER_NAME = "<aks-cluster-name>"
        AKS_RESOURCE_GROUP = "<resource-group>"
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout code from GitHub
                git branch: 'main', url: 'https://github.com/adarshdev-cell/python_app.git'
            }
        }
        stage('Test') {
            steps {
                // Run tests (optional; add tests if applicable)
                sh 'echo "No tests configured for this Python app"'
            }
        }
        stage('Build Docker Image') {
            steps {
                // Build Docker image
                sh "docker build -t ${DOCKER_IMAGE}:${env.BUILD_NUMBER} ."
            }
        }
        stage('Push Docker Image') {
            steps {
                // Login to Docker Hub and push the image
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh "docker push ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
            }
        }
        stage('Deploy to AKS') {
            steps {
                // Use Azure CLI to set AKS context and deploy
                withCredentials([azureServicePrincipal('azure-credentials')]) {
                    sh '''
                    az login --service-principal -u $AZURE_CREDENTIALS_CLIENT_ID -p $AZURE_CREDENTIALS_CLIENT_SECRET --tenant $AZURE_CREDENTIALS_TENANT_ID
                    az aks get-credentials --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME
                    kubectl set image deployment/my-python-app my-python-app=${DOCKER_IMAGE}:${env.BUILD_NUMBER} --namespace default
                    '''
                }
            }
        }
    }
    post {
        always {
            // Clean up Docker images on Jenkins agent
            sh "docker rmi ${DOCKER_IMAGE}:${env.BUILD_NUMBER} || true"
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}