pipeline {
    agent any

    environment {
        IMAGE_NAME = "flask-app"
        IMAGE_TAG = "v1.0"
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Fetching code...'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh '''
                    docker build -t $IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }

        stage('Run Trivy Vulnerability Scan') { 
            steps {
                echo 'Running Trivy vulnerability scan...'
                sh '''
                    trivy image --severity HIGH,CRITICAL --format JSON --output trivy_scan_report.txt --exit-code 1 $IMAGE_NAME:$IMAGE_TAG || true
                '''
            }
        }

        stage('Archive Trivy Scan Report') {
            steps {
                echo 'Archiving Trivy scan report...'
                archiveArtifacts artifacts: 'trivy_scan_report.txt', fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully, even if vulnerabilities exist!'
        }
    }
}
