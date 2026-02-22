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
                    mkdir -p "$WORKSPACE/.npm"
                    export NPM_CONFIG_CACHE="$WORKSPACE/.npm"
                    npm ci --cache "$WORKSPACE/.npm"
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
                    mkdir -p "$WORKSPACE/.npm"
                    export NPM_CONFIG_CACHE="$WORKSPACE/.npm"
                    npm ci --cache "$WORKSPACE/.npm" || true
                    npm test
                '''
            }
        }

        stage('E2E') {
           
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.58.2-jammy'
                    reuseNode true
                  
                }
            }
            steps {
                sh '''
                    mkdir -p "$WORKSPACE/.npm"
                    export NPM_CONFIG_CACHE="$WORKSPACE/.npm"

                    npm install --cache "$WORKSPACE/.npm" serve

                    node_modules/.bin/serve -s build &

                    sleep 10  # give server time to start

                    npx playwright test --reporter=html
                '''
            }
        }
    }

    post {
        always {
            // Publish test results
            junit testResults: 'test-results/junit.xml', allowEmptyResults: true
                // Publish Playwright HTML report
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}