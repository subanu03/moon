pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "sunmoon-python-app"    // Name of your Docker image
        GIT_REPO = "https://github.com/subanu03/moon.git"
    }

    triggers {
        // GitHub webhook triggers this automatically
        pollSCM('H/1 * * * *')  // Backup: polls every minute
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: "${env.GIT_REPO}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    docker.build("${env.DOCKER_IMAGE}:latest")
                }
            }
        }

        stage('Stop Existing Container') {
            steps {
                script {
                    // Stop and remove old container if it exists
                    sh "docker rm -f sunmoon-container || true"
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    // Run new container
                    sh "docker run -d --name sunmoon-container -p 8080:8080 ${env.DOCKER_IMAGE}:latest"
                }
            }
        }

        stage('Cleanup') {
            steps {
                echo "Python Docker app deployed successfully!"
            }
        }
    }

    post {
        failure {
            echo "Build failed! Check Jenkins logs."
        }
    }
}
