pipeline {
    agent any

    environment {
        GHCR_REGISTRY = "ghcr.io"
        OWNER = "suriya1776"
        MS1 = "accounting"
        MS2 = "ad"
        VERSION = "latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Login to GHCR') {
            steps {
                withCredentials([string(credentialsId: 'ghcr-token', variable: 'GHCR_PAT')]) {
                    sh '''
                        echo "$GHCR_PAT" | docker login $GHCR_REGISTRY -u $OWNER --password-stdin
                    '''
                }
            }
        }

        stage('Build and Push Microservice 1') {
            steps {
                script {
                    // Run docker commands inside a container that has Docker CLI available.
                    docker.image('docker:20.10.16').inside('--privileged -v /var/run/docker.sock:/var/run/docker.sock') {
                        sh '''
                            docker build -t $GHCR_REGISTRY/$OWNER/$MS1:$VERSION -f src/$MS1/Dockerfile src/$MS1
                            docker push $GHCR_REGISTRY/$OWNER/$MS1:$VERSION
                        '''
                    }
                }
            }
        }

        stage('Build and Push Microservice 2') {
            steps {
                script {
                    docker.image('docker:20.10.16').inside('--privileged -v /var/run/docker.sock:/var/run/docker.sock') {
                        sh '''
                            docker build -t $GHCR_REGISTRY/$OWNER/$MS2:$VERSION -f src/$MS2/Dockerfile src/$MS2
                            docker push $GHCR_REGISTRY/$OWNER/$MS2:$VERSION
                        '''
                    }
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
