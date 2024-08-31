pipeline {
    agent any
    environment {
        MINIKUBE_VERSION = "v1.33.1"
        KUBECTL_VERSION = "v1.25.0"
        DOCKER_HUB_REPO = 'malikdrote'
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials'
    }
    stages {
        stage('Install Minikube and Dependencies') {
            steps {
                script {
                   
                    sh '''
                        curl -Lo minikube https://storage.googleapis.com/minikube/releases/${MINIKUBE_VERSION}/minikube-linux-amd64
                        chmod +x minikube
                        sudo mv minikube /usr/local/bin/

                        
                        curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl
                        chmod +x kubectl
                        sudo mv kubectl /usr/local/bin/
                    '''
                }
            }
        }
        stage('Start Minikube') {
            steps {
                script {
                    sh '''
                        
                        minikube start --driver=docker
                    '''
                }
            }
        }
        stage('Build and Push Docker Images') {
            steps {
                script {
                    dir('frontend') {
                        docker.build("${DOCKER_HUB_REPO}/frontend:latest").push('latest')
                    }
                    dir('backend') {
                        docker.build("${DOCKER_HUB_REPO}/backend:latest").push('latest')
                    }
                }
            }
        }
        stage('Deploy to Minikube') {
            steps {
                script {
                    sh '''
                        # Use Minikube's Docker environment
                        eval $(minikube -p minikube docker-env)
                        
                        # Apply Kubernetes manifests
                        kubectl apply -f k8s/mysql-deployment.yml
                        kubectl apply -f k8s/frontend-deployment.yml
                        kubectl apply -f k8s/backend-deployment.yml

                        # Wait for the deployments to be ready
                        kubectl rollout status deployment/frontend-deployment
                        kubectl rollout status deployment/backend-deployment
                    '''
                }
            }
        }
        stage('Cleanup') {
            steps {
                script {
                    sh '''
                        
                        minikube stop
                        minikube delete
                    '''
                }
            }
        }
    }
    post {
        always {
            
            deleteDir()
        }
    }
}
