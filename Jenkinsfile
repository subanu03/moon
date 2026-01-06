pipeline {
    agent any

    environment {
        GIT_REPO = "https://github.com/subanu03/moon.git"
        APP_NAME = "sunmoon-python-app"
    }

    triggers {
        pollSCM('H/1 * * * *')  // Poll every minute as backup
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: "${env.GIT_REPO}"
                script {
                    COMMIT_HASH = bat(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    echo "Git commit hash: ${COMMIT_HASH}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    IMAGE_TAG = "${APP_NAME}:${COMMIT_HASH}"
                    echo "Building Docker image ${IMAGE_TAG}"
                    bat "docker build -t ${IMAGE_TAG} ."
                }
            }
        }

        stage('Stop Previous Container') {
            steps {
                script {
                    bat """
                    docker stop ${APP_NAME}-container || exit 0
                    docker rm ${APP_NAME}-container || exit 0
                    """
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    bat "docker run -d --name ${APP_NAME}-container -p 8080:8080 ${IMAGE_TAG}"
                }
            }
        }

        stage('Show Container Logs') {
            steps {
                script {
                    bat "docker logs ${APP_NAME}-container --tail 20"
                }
            }
        }

        stage('Cleanup Old Docker Images') {
            steps {
                script {
                    bat "docker image prune -f"
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
