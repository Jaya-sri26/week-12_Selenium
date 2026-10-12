pipeline {
    agent any


    stages {
        stage('Run Selenium Tests with pytest') {
            steps {
                echo "Running Selenium Tests using pytest"

                // Install Python dependencies
                bat 'pip install -r requirements.txt'

                // Start Flask app in background
                bat 'start /B python app.py'

                // Wait for Flask to start
                bat 'ping 127.0.0.1 -n 5 > nul'

                // Run Selenium tests
                bat 'python -m pytest -v'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker Image"
                bat "docker build -t jaya02sri26/selenium:seleniumimage ."
            }
        }

        stage('Docker Login') {
            steps {
                echo "Logging into Docker Hub"
                bat 'docker login -u Jaya02sri26 -p Jaya@sri2'
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                echo "Pushing Docker Image to Docker Hub"
                bat "docker push jaya02sri26/selenium:seleniumimage"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "Deploying to Kubernetes"
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    bat 'kubectl apply -f deployment.yaml --validate=false'
                    bat 'kubectl apply -f service.yaml'
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Please check the logs.'
        }
    }
}
