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
                }
            }
            steps {
                sh '''
                    test -f build/index.html
                    npm install -g serve
                    serve -s build &
                    sleep 2
                    npx playwright install
                    npx playwright test
                '''
            }
        }
    }
    post {
        always {
            junit 'test-results/junit.xml'
            sh '''
                docker system prune -af --volumes || true
                rm -rf node_modules .npm .npm-cache || true
            '''
        }
    }
}
