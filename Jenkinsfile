pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/VictoriaTodd/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || true' // Allows pipeline to continue despite test failures
            }
            post {
                always {
                    emailext (
                        subject: "Test Stage - ${currentBuild.currentResult}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                        body: "The Run Tests stage completed with status: ${currentBuild.currentResult}.\n\nSee attached log for details.",
                        to: "${env.NOTIFY_EMAIL}",
                        attachLog: true
                    )
                }
            }
        }

        stage('Generate Coverage Report') {
            steps {
                // Ensure coverage report exists
                sh 'npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true' // This will show known CVEs in the output
            }
            post {
                always {
                    emailext (
                        subject: "Security Scan Stage - ${currentBuild.currentResult}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                        body: "The NPM Audit (Security Scan) stage completed with status: ${currentBuild.currentResult}.\n\nSee attached log for details.",
                        to: "${env.NOTIFY_EMAIL}",
                        attachLog: true
                    )
                }
            }
        }
        }
    }
}
