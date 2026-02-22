pipeline {
    agent any

    stages {

        stage('Build') {
            agent {
                docker {
                    image 'node:18-bullseye'
                    reuseNode true
                    args '-u root:root'  // run container as root
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
                  image 'mcr.microsoft.com/playwright:v1.42.1-focal' // Chromium-only image
                    reuseNode true
                    args '-u root:root'
                }
            }
            steps {
                sh '''
                    npm ci
                    npm install serve

                    # Start the React build folder in the background
                    node_modules/.bin/serve -s build &

                    sleep 3  # give server time to start

                    # Run Playwright E2E tests using Chromium
                    npx playwright test --project=chromium
                '''
            }
        }
    }

    post {
        always {
            // Publish test results
            junit testResults: 'test-results/junit.xml', allowEmptyResults: true

        }
    }
}