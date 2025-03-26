pipeline {
    agent any

    environment {
        GHCR_REGISTRY = "ghcr.io"
        OWNER = "suriya1776"
        MS1 = "accounting"
        MS1 = "ad"
        VERSION = "latest"
        GHCR_PAT = credentials('ghcr-token')  // Use a stored Jenkins secret
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Login to GHCR') {
            steps {
                script {
                    sh "echo $GHCR_PAT | docker login $GHCR_REGISTRY -u $OWNER --password-stdin"
                }
            }
        }

        stage('Build and Push Microservice 1') {
            steps {
                script {
                    sh """
                        docker build -t $GHCR_REGISTRY/$OWNER/$MS1:$VERSION -f src/$MS1/Dockerfile .
                        docker push $GHCR_REGISTRY/$OWNER/$MS1:$VERSION
                    """
                }
            }
        }

        stage('Build and Push Microservice 2') {
            steps {
                script {
                    sh """
                        docker build -t $GHCR_REGISTRY/$OWNER/$MS2:$VERSION -f src/$MS2/Dockerfile /src/$MS2
                        docker push $GHCR_REGISTRY/$OWNER/$MS2:$VERSION
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Microservices built and pushed to GHCR successfully!"
        }
        failure {
            echo "❌ Build failed. Check logs."
        }
    }
}
