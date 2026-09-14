pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    tools {
        nodejs 'node'
    }

    environment {
        CI = 'true'
        IMAGE_TAG = 'v1.0'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm

                script {
                    if (env.BRANCH_NAME == 'main') {
                        env.IMAGE_NAME = 'nodemain'
                        env.HOST_PORT = '3000'
                        env.CONTAINER_NAME = 'node-main'
                    } else if (env.BRANCH_NAME == 'dev') {
                        env.IMAGE_NAME = 'nodedev'
                        env.HOST_PORT = '3001'
                        env.CONTAINER_NAME = 'node-dev'
                    } else {
                        error("Unsupported branch: ${env.BRANCH_NAME}")
                    }
                }
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Docker build') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f "$CONTAINER_NAME" 2>/dev/null || true

                    docker run -d \
                      --name "$CONTAINER_NAME" \
                      --expose 3000 \
                      -p "$HOST_PORT":3000 \
                      "$IMAGE_NAME:$IMAGE_TAG"
                '''
            }
        }
    }
}
