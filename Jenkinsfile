pipeline {
    agent any  // Use any available agent
    environment {
        SNYK_TOKEN = credentials('d3ce9f67-6d1d-4bb9-8f7f-aebe2c82510f')  // Obtain Snyk API Token from Jenkins credentials
    }
    stages {
        stage('Install Dependencies') {
            steps {
                script {
                    sh 'docker run --rm -v /root/.npm:/root/.npm -w /workspace node:16 npm install --save'
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    sh 'docker run --rm -v /root/.npm:/root/.npm -w /workspace node:16 npm install -g snyk'
                    sh 'docker run --rm -v /root/.npm:/root/.npm -w /workspace node:16 snyk test'
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    sh 'docker run --rm -v /root/.npm:/root/.npm -w /workspace node:16 npm run build'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh 'docker run --rm -v /root/.npm:/root/.npm -w /workspace node:16 npm test'
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
