
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building Flask application'

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
                echo 'Starting MongoDB for tests'

                sh '''
                    docker network rm flask-ci-net 2>/dev/null || true
                    docker rm -f flask-mongo 2>/dev/null || true

                    docker network create flask-ci-net

                    docker run -d \
                    --name flask-mongo \
                    --network flask-ci-net \
                    mongo:7

                    READY=0

                    for i in $(seq 1 30); do
                        if docker exec flask-mongo mongosh --quiet --eval "db.adminCommand('ping')" >/dev/null 2>&1; then
                            READY=1
                            break
                        fi
                        sleep 2
                    done

                    if [ "$READY" -ne 1 ]; then
                        echo "MongoDB failed to start"
                        exit 1
                    fi

                    docker run --rm \
                    --network flask-ci-net \
                    -e MONGO_URI=mongodb://flask-mongo:27017/testdb \
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
        always {
            sh '''
                docker rm -f flask-mongo 2>/dev/null || true
                docker network rm flask-ci-net 2>/dev/null || true
            '''
        }

        success {
                    success {
            echo 'Pipeline completed successfully!'

            emailext(
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Flask CI/CD pipeline completed successfully.\nBuild URL: ${env.BUILD_URL}",
                to: 'heroviredcluster@gmail.com'
            )
        }

        failure {
            echo 'Pipeline failed!'

            emailext(
                subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Flask CI/CD pipeline failed.\nPlease check the Jenkins console output.\nBuild URL: ${env.BUILD_URL}",
                to: 'heroviredcluster@gmail.com'
            )
        }
        }

        failure {
                    success {
            echo 'Pipeline completed successfully!'

            emailext(
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Flask CI/CD pipeline completed successfully.\nBuild URL: ${env.BUILD_URL}",
                to: 'heroviredcluster@gmail.com'
            )
        }

        failure {
            echo 'Pipeline failed!'

            emailext(
                subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Flask CI/CD pipeline failed.\nPlease check the Jenkins console output.\nBuild URL: ${env.BUILD_URL}",
                to: 'heroviredcluster@gmail.com'
            )
        }
        }
    }
}

