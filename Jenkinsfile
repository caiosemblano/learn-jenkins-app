pipeline {
    agent {
        docker {
            image 'node:18'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Environment Check') {
            steps {
                sh '''
                    echo "=== Environment Verification ==="
                    node --version
                    npm --version
                    echo "================================"
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    echo "Installing dependencies..."
                    npm ci
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                    echo "Building application..."
                    npm run build
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    echo "Running tests..."
                    npm test -- --watchAll=false --passWithNoTests
                '''
            }
        }

        stage('Archive Artifacts') {
            steps {
                sh '''
                    echo "Archiving build artifacts..."
                    ls -la build/
                '''
                archiveArtifacts artifacts: 'build/**', allowEmptyArchive: true
                junit 'test-results/junit.xml'
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed'
        }
        success {
            echo '✅ Pipeline completed successfully'
        }
        failure {
            echo '❌ Pipeline failed'
        }
    }
}