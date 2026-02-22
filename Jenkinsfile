pipeline {
    agent any

    environment {
        NODE_MODULES = "${WORKSPACE}/node_modules"
    }

    stages {

        stage('Build') {
            agent {
                docker {
                    image 'node:18-bullseye'
                    reuseNode true
                    args '-u root:root'
                }
            }
            steps {
                sh '''
                    npm ci
                    npm run build
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-bullseye'
                    reuseNode true
                    args '-u root:root'
                }
            }
            steps {
                sh '''
                    mkdir -p test-results
                    npm test
                '''
            }
        }

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.42.1-focal'  // smaller Chromium-only image
                    reuseNode true
                    args '-u root:root'
                }
            }
            steps {
                sh '''
                    # Reuse node_modules from Build stage
                    export NODE_PATH=${NODE_MODULES}
                    npm install serve

                    # Serve the build folder in the background
                    node_modules/.bin/serve -s build &

                    sleep 3  # wait for server to start

                    # Run Playwright E2E tests (Chromium only)
                    npx playwright install chromium --with-deps
                    npx playwright test --project=chromium
                '''
            }
        }
    }

    post {
        always {
            // Publish JUnit results
            junit testResults: 'test-results/junit.xml', allowEmptyResults: true
        }
    }
}