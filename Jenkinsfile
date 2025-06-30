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
                // If you use GitHub, use:
                // git url: 'https://github.com/YourRepo/sample-flask-app.git'
                // For local Jenkins workspace testing, skip this step.
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
                    trivy image --severity HIGH,CRITICAL --format table --output trivy_scan_report.txt --exit-code 1 $IMAGE_NAME:$IMAGE_TAG || true
                '''
            }
        }

        stage('Archive Trivy Scan Report') {
            steps {
                echo 'Archiving Trivy scan report...'
                archiveArtifacts artifacts: 'trivy_scan_report.txt', fingerprint: true
            }
        }

        stage('Fail Build on Critical Vulnerabilities') {
            steps {
                echo 'Checking if Critical vulnerabilities exist...'
                script {
                    def result = sh(script: "grep CRITICAL trivy_scan_report.txt | wc -l", returnStdout: true).trim()
                    if (result.toInteger() > 0) {
                        error "Critical vulnerabilities found! Failing build."
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully with no critical vulnerabilities!'
        }
        failure {
            echo 'Pipeline failed due to critical vulnerabilities or build error.'
        }
    }
}
