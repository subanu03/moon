pipeline {
    agent any

    triggers {
        githubPush()  // Trigger on GitHub push
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
                bat 'python -m venv venv'
                bat 'venv\\Scripts\\activate && pip install -r requirements.txt'
            }
        }

        stage('Run Python Script') {
            steps {
                bat 'venv\\Scripts\\activate && python app.py'
            }
        }
    }
}
