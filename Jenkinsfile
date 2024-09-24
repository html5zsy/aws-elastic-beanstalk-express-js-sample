pipeline {
    agent {
        docker {
            image 'node:16'  // 使用 Node 16 Docker 镜像作为构建代理
            args '-v /root/.npm:/root/.npm'  // 持久化 npm 缓存以加速构建
        }
    }
    environment {
        SNYK_TOKEN = credentials('d3ce9f67-6d1d-4bb9-8f7f-aebe2c82510f')  // 从 Jenkins 凭据管理获取 Snyk API Token
    }
    stages {
        stage('Install Dependencies') {
            steps {
                script {
                    docker.image('node:16').inside('-v /root/.npm:/root/.npm') {
                        // 安装依赖
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
                        // 安装 Snyk
                        sh 'npm install -g snyk'  // 全局安装 Snyk
                        def snykVersion = sh(script: 'snyk --version', returnStdout: true).trim()
                        echo "Snyk version: ${snykVersion}"

                        // 运行 Snyk 扫描
                        def snykResult = sh(script: 'snyk test', returnStatus: true)
                        
                        // 如果发现关键漏洞，停止流水线
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
                        // 进行构建
                        sh 'npm run build'  // 示例构建步骤
                    }
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    docker.image('node:16').inside('-v /root/.npm:/root/.npm') {
                        // 运行测试（如果有）
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
            // 构建失败时通知
            echo 'Pipeline failed due to high-severity vulnerabilities or other issues.'
        }
    }
}
