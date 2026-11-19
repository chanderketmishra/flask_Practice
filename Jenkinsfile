
pipeline {
    agent any

    stages {
       
stage('Build') {
    steps {
        echo 'Creating Python virtual environment'
        sh 'python3 -m venv .venv'
        sh '.venv/bin/python -m pip install --upgrade pip'
        sh '.venv/bin/python -m pip install -r requirements.txt'
    }
}

            }
        }

        stage('Test') {
            
stage('Test') {
    steps {
        echo 'Running Flask application tests'
        sh '.venv/bin/python -m pip install pytest'
        sh '.venv/bin/python -m pytest -v'
    }
}

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
