pipeline {
    agent any

    environment {
        IMAGE_NAME = 'nodejs-docker-app'
        CONTAINER_NAME = 'nodejs-docker-container'
        APP_PORT = '3000'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh '''
                    docker build -t $IMAGE_NAME:$BUILD_NUMBER -t $IMAGE_NAME:latest .
                '''
            }
        }

        stage('Run Container') {
            steps {
                echo 'Removing old container if it exists...'
                sh '''
                    docker rm -f $CONTAINER_NAME || true
                '''

                echo 'Running new container...'
                sh '''
                    docker run -d \
                    --name $CONTAINER_NAME \
                    -p $APP_PORT:3000 \
                    $IMAGE_NAME:$BUILD_NUMBER
                '''
            }
        }

        stage('Verify') {
            steps {
                echo 'Verifying running container...'
                sh '''
                    docker ps --filter "name=$CONTAINER_NAME"
                    curl -I http://localhost:$APP_PORT || true
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully. Node.js Docker app is running on port 3000.'
        }

        failure {
            echo 'Pipeline failed. Check Jenkins console output.'
        }
    }
}
