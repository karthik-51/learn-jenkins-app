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
                    export NPM_CONFIG_CACHE="$WORKSPACE/.npm"
                    mkdir -p "$NPM_CONFIG_CACHE"
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
                    export NPM_CONFIG_CACHE="$WORKSPACE/.npm"
                    mkdir -p "$NPM_CONFIG_CACHE" test-results
                    npm test
                '''
            }
        }

        stage('E2E') {
            when {
                expression { return env.RUN_E2E == 'true' }
            }
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:latest'
                    reuseNode true
                  
                }
            }
            steps {
                sh '''
                    export NPM_CONFIG_CACHE="$WORKSPACE/.npm"
                    mkdir -p "$NPM_CONFIG_CACHE"
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