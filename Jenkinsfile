pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "pythonapp:latest"
        GIT_REPO = "https://github.com/yourusername/your-repo.git"
    }

    triggers {
        // Git webhook will trigger automatically
        pollSCM('H/1 * * * *')
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
                    docker.build("${env.DOCKER_IMAGE}")
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    sh "docker rm -f pythonapp-container || true"
                    sh "docker run -d --name pythonapp-container -p 8080:8080 ${env.DOCKER_IMAGE}"
                }
            }
        }

        stage('Cleanup') {
            steps {
                echo "Python app deployed successfully!"
            }
        }
    }

    post {
        failure {
            echo "Build failed! Check Jenkins logs."
        }
    }
}
