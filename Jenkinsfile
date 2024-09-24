pipeline {
    agent any  // Use any available agent
    environment {
        SNYK_TOKEN = credentials('d3ce9f67-6d1d-4bb9-8f7f-aebe2c82510f')  // Snyk API token
    }
    stages {
        stage('Install Dependencies') {
            steps {
                script {
                    docker.image('node:16').inside('-v /root/.npm:/root/.npm') {
                        def installResult = sh(script: 'npm install --save', returnStatus: true)
                        if (installResult != 0) {
                            error "npm install failed, halting build."
                        }
                    }
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    docker.image('node:16').inside('-v /root/.npm:/root/.npm') {
                        sh 'npm install -g snyk'  // Global install of Snyk
                        def snykVersion = sh(script: 'snyk --version', returnStdout: true).trim()
                        echo "Snyk version: ${snykVersion}"
                        def snykResult = sh(script: 'snyk test', returnStatus: true)
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
                    docker.image('node:16').inside('-v /root/.npm:/root/.npm') {
                        sh 'npm run build'
                    }
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    docker.image('node:16').inside('-v /root/.npm:/root/.npm') {
                        def testResult = sh(script: 'npm test', returnStatus: true)
                        if (testResult != 0) {
                            error "Tests failed, halting build."
                        }
                    }
                }
            }
        }
    }
    post {
        failure {
            echo 'Pipeline failed due to high-severity vulnerabilities or other issues.'
        }
    }
}
