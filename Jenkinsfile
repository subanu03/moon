pipeline {
    agent any

    environment {
        GIT_REPO = "https://github.com/subanu03/moon.git"
        APP_NAME = "sunmoon-trigger-app"
    }

    triggers {
        // Webhook trigger + backup poll
        pollSCM('H/1 * * * *')
    }

    stages {

        stage('Checkout Code') {
            steps {
                // Pull latest code and get commit hash
                git branch: 'main', url: "${env.GIT_REPO}"
                script {
                    COMMIT_HASH = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
                    echo "Git commit hash: ${COMMIT_HASH}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Tag image with commit hash
                    IMAGE_TAG = "${APP_NAME}:${COMMIT_HASH}"
                    echo "Building Docker image ${IMAGE_TAG}"
                    docker.build(IMAGE_TAG)
                }
            }
        }

        stage('Stop Previous Container') {
            steps {
                script {
                    // Stop previous container gracefully
                    sh """
                    if [ \$(docker ps -q -f name=${APP_NAME}-container) ]; then
                        echo 'Stopping old container...'
                        docker stop ${APP_NAME}-container
                        docker rm ${APP_NAME}-container
                    fi
                    """
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    // Run container with new image
                    sh """
                    docker run -d --name ${APP_NAME}-container -p 8080:8080 ${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Show Container Logs') {
            steps {
                script {
                    sh "docker logs ${APP_NAME}-container --tail 20"
                }
            }
        }

        stage('Cleanup Old Docker Images') {
            steps {
                script {
                    // Remove dangling images (not tagged)
                    sh "docker image prune -f"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful: ${IMAGE_TAG}"
        }
        failure {
            echo "❌ Deployment failed. Check logs."
        }
    }
}
