pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                    docker run --rm \
                    -v "$WORKSPACE":/app \
                    -w /app \
                    python:3.12 \
                    sh -c "
                        pip install -r requirements.txt
                    "
                '''
            }
        }
    }
}
