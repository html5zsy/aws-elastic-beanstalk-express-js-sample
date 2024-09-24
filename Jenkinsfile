pipeline {
    // agent {
    //     docker {
    //         image 'node:16'  // Using Node 16 Docker image as the build agent
    //         args '-v /root/.npm:/root/.npm'  // Persist npm cache to speed up builds
    //     }
    // }
    agent any
    environment {
        // from Jenkins credential management Snyk API Token
        SNYK_TOKEN = credentials('d3ce9f67-6d1d-4bb9-8f7f-aebe2c82510f')
    }
    stages {
        stage('Install Dependencies') {
            steps {
                script {
                    docker.image('node:16').inside('-v /root/.npm:root/.npm') {
                        // Install dependencies
                        sh 'npm install --save'
                    }
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    docker.image('node:16').inside('-v /root/.npm:root/.npm') {
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
        }
        stage('Build') {
            steps {
                script {
                    docker.image('node:16').inside('-v /root/.npm:root/.npm') {
                        // Add your build steps here
                        sh 'npm run build'  // Example build step
                    }
                }
            }
        }
        // You can add more stages for testing, deployment, etc.
        stage('Test') {
            steps {
                script {
                    docker.image('node:16').inside('-v /root/.npm:root/.npm') {
                        // Run tests (if any)
                        sh 'npm test'
                    }
                }
            }
        }
    }
    post {
        failure {
            // Notify on failure
            echo 'Pipeline failed due to high-severity vulnerabilities or other issues.'
        }
    }
}
