pipeline {
    agent any
    environment {
        DOCKER_HUB_REPO = 'malikdrote'
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials'
    }
    stages {
        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    script {
                        docker.build("${DOCKER_HUB_REPO}/frontend:latest").push('latest')
                    }
                }
            }
        }
        stage('Build Backend') {
            steps {
                dir('backend') {
                    script {
                        docker.build("${DOCKER_HUB_REPO}/backend:latest").push('latest')
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                // Set kubectl to use Minikube's context
            sh 'kubectl config use-context minikube'

            // Deploy the mysql database, frontend, and backend using the Kubernetes YAML files
            sh 'kubectl apply -f k8s/mysql-deployment.yml'
            sh 'kubectl apply -f k8s/frontend-deployment.yml'
            sh 'kubectl apply -f k8s/backend-deployment.yml'
            }
        }
    }
}
