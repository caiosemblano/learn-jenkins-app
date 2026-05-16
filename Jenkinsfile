pipeline {
    agent any

    environment {
        CI = 'true'
    }

    stages {
        stage('Build') {
            steps {
                bat '''
                    node --version
                    npm --version
                    npm ci
                    npm run build
                '''
            }
        }

        stage('Test') {
            steps {
                bat '''
                    npm test
                '''
            }
        }

        stage('E2E') {
            steps {
                bat '''
                    npm install serve
                    npx playwright install chromium
                    npx playwright test
                '''
            }
        }
    }

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
