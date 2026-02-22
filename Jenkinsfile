pipeline {
    agent any
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -ltr
                '''
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    test -f build/index.html
                    npm test
                '''
            }
        }
        stage('E2E') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-u root:root'
                }
            }
            steps {
                sh '''
                    # Install system dependencies for browser
                    apk add --no-cache libstdc++ libx11 libxrandr libxinerama libxi libxext libxcursor
                    
                    test -f build/index.html
                    npm install -g serve
                    serve -s build &
                    sleep 2
                    
                    # Install Playwright with browser dependencies
                    npx playwright install --with-deps chromium
                    
                    # Run tests (junit reporter is configured in playwright.config.js)
                    npx playwright test
                '''
            }
        }
    }
    post {
        always {
            junit testResults: 'test-results/junit.xml', allowEmptyResults: true
            sh '''
                docker system prune -af --volumes || true
                rm -rf node_modules .npm .npm-cache || true
            '''
        }
    }
}
