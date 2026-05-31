pipeline {
    agent any

    tools {
        nodejs 'node18' // Carrega o Node configurado globalmente no Jenkins
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

        stage('Tests') {
            parallel {
                stage('Unit tests') {
                    steps {
                        bat 'npm test'
                    }
                    post {
                        always {
                            junit 'jest-results/junit.xml'
                        }
                    }
                }

                stage('E2E') {
                    steps {
                        bat '''
                            npm install serve
                            start /B node_modules\\.bin\\serve -s build
                            timeout /t 10
                            npx playwright test --reporter=html
                        '''
                    }
                    post {
                        always {
                            publishHTML([
                                allowMissing: false, 
                                alwaysLinkToLastBuild: false, 
                                keepAll: false, 
                                reportDir: 'playwright-report', 
                                reportFiles: 'index.html', 
                                reportName: 'Playwright HTML Report', 
                                reportTitles: '', 
                                useWrapperFileDirectly: true
                            ])
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                bat '''
                    npm install netlify-cli
                    node_modules\\.bin\\netlify --version
                '''
            }
        }
    }
}