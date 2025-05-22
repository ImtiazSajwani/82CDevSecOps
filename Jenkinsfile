pipeline {
    agent any
    
    environment {
        SONAR_SCANNER_VERSION = '4.7.0.2747'  // Compatible with Java 11
        SONAR_SCANNER_HOME = "${WORKSPACE}/sonar-scanner-${SONAR_SCANNER_VERSION}-linux"
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ImtiazSajwani/82CDevSecOps.git'
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
                        # Check Java version
                        echo "Java version:"
                        java -version
                        
                        # Install unzip if not available
                        if ! command -v unzip &> /dev/null; then
                            echo "Installing unzip..."
                            sudo apt-get update -qq
                            sudo apt-get install -y unzip
                        fi
                        
                        # Download compatible SonarScanner version
                        echo "Downloading SonarScanner ${SONAR_SCANNER_VERSION}..."
                        wget -q https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SONAR_SCANNER_VERSION}-linux.zip
                        
                        # Extract SonarScanner
                        echo "Extracting SonarScanner..."
                        unzip -q sonar-scanner-cli-${SONAR_SCANNER_VERSION}-linux.zip
                        
                        # Make SonarScanner executable
                        chmod +x ${SONAR_SCANNER_HOME}/bin/sonar-scanner
                        
                        # Run SonarScanner
                        echo "Running SonarCloud analysis..."
                        ${SONAR_SCANNER_HOME}/bin/sonar-scanner \\
                            -Dsonar.projectKey=ImtiazSajwani_82CDevSecOps \\
                            -Dsonar.organization=imtiazsjwani \\
                            -Dsonar.host.url=https://sonarcloud.io \\
                            -Dsonar.login=${SONAR_TOKEN} \\
                            -Dsonar.sources=. \\
                            -Dsonar.exclusions=node_modules/**,test/**,coverage/**,sonar-scanner-*/** \\
                            -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \\
                            -Dsonar.projectName="NodeJS Goof Vulnerable App" \\
                            -Dsonar.projectVersion=1.0
                    '''
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline execution completed'
            sh 'rm -f sonar-scanner-cli-*.zip'
            sh 'rm -rf sonar-scanner-*/'
        }
        success {
            echo 'Pipeline executed successfully'
        }
        failure {
            echo 'Pipeline execution failed'
        }
    }
}
