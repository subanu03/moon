pipeline {
    agent any

    triggers {
        githubPush()
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/subanu03/moon.git',
                    credentialsId: 'github-pat'
            }
        }

        stage('Install Python Dependencies') {
            steps {
                // Create virtual environment
                bat 'python -m venv venv'

                // Upgrade pip inside venv
                bat 'venv\\Scripts\\python -m pip install --upgrade pip'

                // Install dependencies from requirements.txt
                bat 'venv\\Scripts\\pip install -r requirements.txt'
            }
        }

        stage('Run Python Script') {
            steps {
                // Run the app using the venv Python
                bat 'venv\\Scripts\\python app.py'
            }
        }
    }
}
