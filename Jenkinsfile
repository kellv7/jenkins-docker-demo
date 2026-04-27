pipeline {
    agent any

    environment {
        REGISTRY = "ghcr.io"
        GITHUB_USERNAME = "kellv7"
        IMAGE_NAME = "jenkins-docker-demo"
        CONTAINER_NAME = "demo-app"
        APP_PORT = "5000"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build \
                  -t $REGISTRY/$GITHUB_USERNAME/$IMAGE_NAME:$BUILD_NUMBER \
                  -t $REGISTRY/$GITHUB_USERNAME/$IMAGE_NAME:latest \
                  .
                '''
            }
        }

        stage('Login to GHCR') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'ghcr-creds',
                    usernameVariable: 'GHCR_USER',
                    passwordVariable: 'GHCR_TOKEN'
                )]) {
                    sh '''
                    echo "$GHCR_TOKEN" | docker login $REGISTRY -u "$GHCR_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                docker push $REGISTRY/$GITHUB_USERNAME/$IMAGE_NAME:$BUILD_NUMBER
                docker push $REGISTRY/$GITHUB_USERNAME/$IMAGE_NAME:latest
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker rm -f $CONTAINER_NAME || true

                docker run -d \
                  --name $CONTAINER_NAME \
                  -p $APP_PORT:5000 \
                  $REGISTRY/$GITHUB_USERNAME/$IMAGE_NAME:latest
                '''
            }
        }
    }
}
