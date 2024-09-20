pipeline {
    agent {
        docker {
            image 'node:16'  // Using Node 16 Docker image as the build agent
            args '-v /root/.npm:/root/.npm'  // Persist npm cache to speed up builds
        }
    }
    stages {
        stage('Install Dependencies') {
            steps {
                script {
                    // Install dependencies
                    sh 'npm install --save'
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    // Run Snyk to scan for vulnerabilities
                    sh 'npm install -g snyk'  // Install Snyk globally
                    def snykResult = sh(script: 'snyk test', returnStatus: true)
                    
                    // Halt the pipeline if critical vulnerabilities are found
                    if (snykResult != 0) {
                        error "Critical vulnerabilities detected, halting build."
                    }
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    // Add your build steps here
                    sh 'npm run build'  // Example build step
                }
            }
        }
        // You can add more stages for testing, deployment, etc.
    }
}
