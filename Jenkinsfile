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
        stage('Run Tests') {
            parallel {
                stage('Test') {
                    steps {
                        bat '''
                            npm test -- --passWithNoTests
                        '''
                    }
                }

                stage('E2E') {
                    steps {
                        bat '''
                            npm install serve
                            npx playwright install chromium
                            npx playwright test --reporter=html
                        '''
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                bat 'echo "Deploying application..."'
            }
        }
    }

    post {
        always {
            junit testResults: 'test-results/*.xml', allowEmptyResults: true
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}
