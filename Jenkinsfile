pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-bullseye'
                    reuseNode true
                    
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
                    
                }
            }
            steps {
                sh '''
                    mkdir -p  test-results
                    npm test
                '''
            }
        }

        stage('E2E') {
           
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.42.1-jammy'
                    reuseNode true
                  
                }
            }
            steps {
                sh '''
                    
                    npm install serve

                    node_modules/.bin/serve -s build &

                    sleep 10  # give server time to start

                    npx playwright test
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