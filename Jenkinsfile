
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Installing Python dependencies'
                sh 'python3 --version'
                sh 'python3 -m pip install -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                echo 'Running Flask application tests'
                sh 'python3 -m pip install pytest'
                sh 'python3 -m pytest -v'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Flask application to staging'
                sh 'mkdir -p staging'
                sh 'cp app.py staging/'
                sh 'cp requirements.txt staging/'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
