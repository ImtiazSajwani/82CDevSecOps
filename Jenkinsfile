pipeline {
    agent any
    
    environment {
        SONAR_SCANNER_VERSION = '4.8.0.2856'
        SONAR_SCANNER_HOME = "${WORKSPACE}/sonar-scanner-${SONAR_SCANNER_VERSION}-linux"
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/imtiazsjwani/82CDevSecOps.git'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Run Tests') {
            steps {
                sh 'npm test || true'
            }
        }
        
        stage('Generate Coverage Report') {
            steps {
                sh 'npm run coverage || true'
            }
        }
        
        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true'
            }
        }
        
        stage('SonarCloud Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    sh '''
                        # Download SonarScanner CLI
                        wget -q https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SONAR_SCANNER_VERSION}-linux.zip
                        
                        # Extract SonarScanner
                        unzip -q sonar-scanner-cli-${SONAR_SCANNER_VERSION}-linux.zip
                        
                        # Make SonarScanner executable
                        chmod +x ${SONAR_SCANNER_HOME}/bin/sonar-scanner
                        
                        # Run SonarScanner
                        ${SONAR_SCANNER_HOME}/bin/sonar-scanner \\
                            -Dsonar.projectKey=imtiazsjwani_82CDevSecOps \\
                            -Dsonar.organization=imtiazsjwani \\
                            -Dsonar.host.url=https://sonarcloud.io \\
                            -Dsonar.login=${SONAR_TOKEN} \\
                            -Dsonar.sources=. \\
                            -Dsonar.exclusions=node_modules/**,test/**,coverage/** \\
                            -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \\
                            -Dsonar.projectName="NodeJS Goof Vulnerable App"
                    '''
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline execution completed'
            // Clean up downloaded files
            sh 'rm -f sonar-scanner-cli-*.zip'
        }
        success {
            echo 'Pipeline executed successfully'
        }
        failure {
            echo 'Pipeline execution failed'
        }
    }
}
