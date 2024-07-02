pipeline {
    agent any

    stages {
        // This is a line comment
        /* 
        line 1
        line 2
        lin3 3
        */
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
                    ls -la
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
                    ls -la
                    node --version 
                    npm --version 
                    npm ci
                    echo "Start running Tests"
                    test -f build/index.html 
                    npm test
                    ls -la
                '''
            }
        }
        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.45.0-jammy'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install serve 
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npx playwright test
                '''
            }
        }
    }
    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
