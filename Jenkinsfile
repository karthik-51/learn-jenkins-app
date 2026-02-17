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
            environment {
                HOME = "${WORKSPACE}"
            }
            steps {
                sh '''
                    npm config set cache $HOME/.npm-cache
                    npm ci
                    npm run build
                '''
            }
        }
    }
}
