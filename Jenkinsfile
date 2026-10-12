pipeline {
    agent any

    environment {
        // Points directly to your Python executable
        PYTHON_EXE = 'C:\Users\23251\AppData\Local\Microsoft\WindowsApps\python.exe'
    }

    stages {
        stage('Run Selenium Tests with pytest') {
            steps {
                echo "Running Selenium Tests using pytest"
                bat '''
                    "%PYTHON_EXE%" -m pip install -r requirements.txt
                    start /B "" "%PYTHON_EXE%" app.py
                    timeout /T 5 /NOBREAK
                    "%PYTHON_EXE%" -m pytest -v
                '''
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
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat "docker login -u Jaya02sri26 -p Jaya@sri26"
                }
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
        always {
            // Stops background Flask/FastAPI app process so it doesn't lock port 5000/8000
            bat 'taskkill /F /IM python.exe 2>nul || exit 0'
        }
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Please check the logs.'
        }
    }
}
