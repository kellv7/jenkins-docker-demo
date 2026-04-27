pipeline {
    agent any

    environment {
        IMAGE_NAME = "demo-app"
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
                  -t $IMAGE_NAME:$BUILD_NUMBER \
                  -t $IMAGE_NAME:latest \
                  .
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
                  $IMAGE_NAME:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }

        failure {
            echo 'Pipeline failed'
        }
    }
}
