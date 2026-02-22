pipeline {
    agent any

    stages {

        stage('Build') {
            agent {
                docker {
                    image 'node:18-bullseye'
                    reuseNode true
                    args '-u root:root'  // run container as root to fix npm permissions
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
                    args '-u root:root'  // run container as root
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
                    image 'mcr.microsoft.com/playwright:v1.42.1-jammy'
                    reuseNode true
                    args '-u root:root'  // ensure full permissions
                }
            }
            steps {
                sh '''
                    # Install dependencies including local serve
                    npm ci
                    npm install serve

                    # Start the React build folder in the background
                    node_modules/.bin/serve -s build &

                    sleep 3  # give the server time to start

                    # Run Playwright E2E tests
                    npx playwright test
                '''
            }
        }
    }

    post {
        always {
            // Publish JUnit test results (works if Jest or Playwright uses JUnit reporter)
            junit testResults: 'test-results/junit.xml', allowEmptyResults: true
        }
    }
}