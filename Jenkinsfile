pipeline {
    agent any

    environment {
        APP_NAME = "flask-app"
        IMAGE_NAME = "flask-app"
        CONTAINER_NAME = "flask-app-container"
        PORT = "5000"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out source code..."
                git branch: 'main', url: 'https://github.com/saundariyah/docker-flask-example.git'
            }
        }

        stage('Build') {
            steps {
                echo "Installing dependencies..."
                sh 'python3 -m pip install --user -r requirements.txt'
                sh 'python3 -m pip install --user pytest'
                sh 'python3 -m pytest --maxfail=1 --disable-warnings -q'

            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                sh './venv/bin/pip install pytest'
                sh './venv/bin/pytest --maxfail=1 --disable-warnings -q'
            }
        }

        stage('Docker Build') {
            steps {
                echo "Building Docker image..."
                sh 'docker build -t ${IMAGE_NAME}:latest .'
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying container..."
                // Stop old container if running
                sh """
                docker ps -q --filter name=${CONTAINER_NAME} | grep -q . && docker stop ${CONTAINER_NAME} && docker rm ${CONTAINER_NAME} || true
                docker run -d --name ${CONTAINER_NAME} -p ${PORT}:5000 ${IMAGE_NAME}:latest
                """
            }
        }
    }

    post {
        always {
            echo 'Cleaning up temporary files...'
            sh 'rm -rf venv'
        }
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Build failed. Check logs in Jenkins.'
        }
    }
}
