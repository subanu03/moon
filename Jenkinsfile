pipeline {
    agent any
    triggers {
        // This triggers the pipeline when GitHub sends a push
        githubPush()
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/subanu03/moon.git', 
            }
        }
        stage('Install Python Dependencies') {
            steps {
                sh 'python3 -m venv venv'
                sh '. venv/bin/activate && pip install -r requirements.txt'
            }
        }
        stage('Run Python Script') {
            steps {
                sh '. venv/bin/activate && python app.py'
            }
        }
    }
}
