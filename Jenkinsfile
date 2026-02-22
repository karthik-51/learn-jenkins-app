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
                    # Ensure tests run in CI mode so junit reporter writes output
                    CI=true npm test
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
                    # list the generated report so Jenkins can archive it and for debugging
                    ls -la playwright-report || true
                '''
            }
        }
    }

    post {
        always {
            // Publish test results (Jest -> junit)
            junit testResults: 'test-results/junit.xml', allowEmptyResults: true
            // Publish Playwright HTML report
            publishHTML([
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'playwright-report',
                reportFiles: 'index.html',
                reportName: 'Playwright HTML Report',
                reportTitles: '',
                useWrapperFileDirectly: true
            ])
        }
    }
}