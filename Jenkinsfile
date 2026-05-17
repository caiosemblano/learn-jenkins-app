pipeline {
    agent any

    environment {
        CI = 'true'
    }

    stages {
        stage('Build') {
            steps {
                bat 'node --version'
                bat 'npm --version'
                bat 'npm ci'
                bat 'npm run build'
            }
        }

        stage('Test') {
            steps {
                bat 'npm test -- --passWithNoTests'
            }
        }

        stage('E2E') {
            steps {
                bat 'npm install serve'
                bat 'npx playwright install chromium'
                bat 'npx playwright test'
            }
        }
    }

    post {
        always {
            junit testResults: 'test-results/*.xml', allowEmptyResults: true
        }
    }
}
