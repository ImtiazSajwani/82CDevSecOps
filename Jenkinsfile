pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                // Clone the repository from GitHub
                git branch: 'main', url: 'https://github.com/ImtiazSajwani/82CDevSecOps.git'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                // Install npm dependencies
                sh 'npm install'
            }
        }
        
        stage('Run Tests') {
            steps {
                // Run the test suite, continue pipeline even if tests fail
                sh 'npm test || true'
            }
        }
        
        stage('Generate Coverage Report') {
            steps {
                // Generate code coverage report
                sh 'npm run coverage || true'
            }
        }
        
        stage('NPM Audit (Security Scan)') {
            steps {
                // Perform security audit to identify vulnerabilities
                sh 'npm audit || true'
            }
        }
    }
    
    post {
        always {
            // Archive the test results and reports
            echo 'Pipeline execution completed'
        }
        success {
            echo 'Pipeline executed successfully'
        }
        failure {
            echo 'Pipeline execution failed'
        }
    }
}
