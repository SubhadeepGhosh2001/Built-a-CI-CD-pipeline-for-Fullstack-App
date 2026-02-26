pipeline {
    agent any

    environment {
        EMAIL = "subhadeepghosh1270@gmail.com"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/SubhadeepGhosh2001/Built-a-CI-CD-pipeline-for-Fullstack-App.git'
            }
        }

        stage('Verify Minikube') {
            steps {
                sh '''
                kubectl cluster-info
                kubectl get nodes
                '''
            }
        }

        stage('Enable Ingress Addon (Minikube Only)') {
            steps {
                sh '''
                minikube addons enable ingress || true
                '''
            }
        }

        stage('Deploy Ingress') {
            steps {
                sh '''
                echo "Applying ingress configuration..."
                kubectl apply -f k8s/ingress.yaml
                '''
            }
        }

        stage('Verify Ingress') {
            steps {
                sh '''
                kubectl get ingress
                kubectl get svc
                kubectl get pods -n ingress-nginx
                '''
            }
        }
    }

    post {
        always {
            emailext (
                subject: "Infra Pipeline ${currentBuild.currentResult}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Infra Deployment Result:

Status: ${currentBuild.currentResult}
Build: ${env.BUILD_NUMBER}
URL: ${env.BUILD_URL}
                """,
                to: "${EMAIL}"
            )
        }
    }
}