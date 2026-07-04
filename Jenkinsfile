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

        stage('Test') {
            environment {
                MONGO_URI = credentials('vinamra-mongo-uri')
                SECRET_KEY = credentials('vinamra-mongo-secret-key')
            }
        
            steps {
                sh '''
                    docker run --rm \
                    -e MONGO_URI="$MONGO_URI" \
                    -e SECRET_KEY="$SECRET_KEY" \
                    -v "$WORKSPACE":/app \
                    -w /app \
                    python:3.12 \
                    sh -c "
                        pip install -r requirements.txt &&
                        pytest test_app.py -v
                    "
                '''
            }
        }

        stage('Deploy') {
            environment {
                MONGO_URI = credentials('vinamra-mongo-uri')
                SECRET_KEY = credentials('vinamra-mongo-secret-key')
            }
        
            steps {
                sh '''
                    docker stop vinamra-flask-app || true
                    docker rm vinamra-flask-app || true
        
                    docker run -d \
                        --name vinamra-flask-app \
                        -p 8000:8000 \
                        -e MONGO_URI="$MONGO_URI" \
                        -e SECRET_KEY="$SECRET_KEY" \
                        -v "$WORKSPACE":/app \
                        -w /app \
                        python:3.12 \
                        sh -c "
                            pip install -r requirements.txt &&
                            python app.py
                        "
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    docker ps
                '''
            }
        }
        
    }
}
