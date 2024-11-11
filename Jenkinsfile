pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub test 1
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Build the Docker image
                sh 'docker build -t flask-app .'
            }
        }

        stage('Cleanup Previous Containers') {
            steps {
                // Stop and remove any existing container named flask-test
                sh 'docker stop flask-test || true'
                sh 'docker rm flask-test || true'

                // Stop any existing container named flask-app from previous deployments
                sh 'docker stop flask-app || true'
                sh 'docker rm flask-app || true'

                // Free up port 5000 if needed
                sh "fuser -k 5000/tcp || true"
            }
        }

        stage('Test') {
            steps {
                // Run the Docker container for testing
                sh 'docker run -d --name flask-test -p 5000:5000 flask-app'
                sh 'sleep 5' // Wait for the container to start

                // Test if the Flask app is responding
                sh 'curl -f http://localhost:5000 || exit 1'

                // Stop and remove the test container after testing
                sh 'docker stop flask-test'
                sh 'docker rm flask-test'
            }
        }

        stage('Deploy') {
            steps {
                // Deploy the container for the actual running service
                // Ensure any old flask-app container is already removed
                sh 'docker run -d --name flask-app -p 5000:5000 flask-app'
            }
        }
    }
}
