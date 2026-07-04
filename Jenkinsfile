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

    post {

        success {
            emailext(
                to: 'parasharvin102@gmail.com',
                subject: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
    Hello,
    
    The Jenkins pipeline completed successfully.
    
    Job Name     : ${env.JOB_NAME}
    Build Number : ${env.BUILD_NUMBER}
    Status       : SUCCESS
    
    The Flask application was:
    ✔ Checked out
    ✔ Built
    ✔ Tested
    ✔ Deployed successfully
    ✔ Verified Deployment
    
    Build URL:
    ${env.BUILD_URL}
    
    Regards,
    Jenkins CI/CD
    """
            )
        }
    
        failure {
            emailext(
                to: 'parasharvin102@gmail.com',
                subject: "❌ FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
    Hello,
    
    The Jenkins pipeline has failed.
    
    Job Name     : ${env.JOB_NAME}
    Build Number : ${env.BUILD_NUMBER}
    Status       : FAILURE
    
    Please review the console output for error details.
    
    Build URL:
    ${env.BUILD_URL}
    
    Regards,
    Jenkins CI/CD
    """
            )
        }
    }
    
}
