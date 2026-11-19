
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building Flask application using Docker'

                sh '''
                    docker run --rm \
                    -v "$WORKSPACE":/app \
                    -w /app \
                    python:3.11-slim \
                    sh -c "python -m pip install --upgrade pip && pip install -r requirements.txt"
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running Flask application tests'

                sh '''
                    docker run --rm \
                    -v "$WORKSPACE":/app \
                    -w /app \
                    python:3.11-slim \
                    sh -c "pip install -r requirements.txt pytest && python -m pytest -v"
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Preparing staging deployment'

                sh '''
                    mkdir -p staging
                    cp app.py staging/
                    cp requirements.txt staging/
                '''
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

