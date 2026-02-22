
// pipeline {
//     agent any

//     stages {

//         stage('Build') {
//             agent {
//                 docker {
//                     image 'node:18-alpine'
//                     reuseNode true
//                 }
//             }
//             steps {
//                 sh '''
//                     npm ci
//                     npm run build
//                 '''
//             }
//         }

//         stage('Test') {
//             agent {
//                 docker {
//                     image 'node:18-alpine'
//                     reuseNode true
//                 }
//             }
//             steps {
//                 sh '''
//                     mkdir -p test-results
//                     npm test
//                 '''
//             }
//         }

//         stage('E2E') {
//             agent {
//                 docker {
//                     image 'node:18-alpine'
//                     reuseNode true
//                 }
//             }
//             steps {
//                 sh '''
//                     apk add --no-cache libstdc++ libx11 libxrandr libxinerama libxi libxext libxcursor
                    
//                     npm install serve
//                     node_modules/.bin/serve -s build &
//                     sleep 3
                    
//                     npx playwright install --with-deps chromium
//                     npx playwright test
//                 '''
//             }
//         }
//     }

//     post {
//         always {
//             junit testResults: 'test-results/junit.xml', allowEmptyResults: true
//         }
//     }
// }

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
            // Publish JUnit test results
            junit testResults: 'test-results/junit.xml', allowEmptyResults: true
        }
    }
}