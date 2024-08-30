pipeline {
    agent any
    environment {
        DOCKER_HUB_REPO = 'malikdrote'
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials'
        MINIKUBE_CERT_DIR = '/var/lib/jenkins/.minikube'
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
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
                withEnv(["KUBECONFIG=${env.MINIKUBE_CERT_DIR}/config"])
                {
                sh '''
                    # Print kubeconfig details for debugging
                    kubectl config view
                    kubectl config get-contexts

                    # Switch to Minikube context
                    kubectl config use-context minikube

                    # Deploy Kubernetes resources
                    kubectl apply -f k8s/mysql-deployment.yml
                    kubectl apply -f k8s/frontend-deployment.yml
                    kubectl apply -f k8s/backend-deployment.yml
                }    '''
            }
        }
    }
}
