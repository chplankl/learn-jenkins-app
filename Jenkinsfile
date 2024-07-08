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
        stage('Run Test') {
            parallel {
                stage('UnitTest') {
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
                    post {
                        always {
                             junit 'jest-results/junit.xml'
                        }
                    }
                }
                /*stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            npm install serve 
                            node_modules/.bin/serve -s build &
                            sleep 30
                                npx playwright test --reporter=html
                            '''
                    }
                     post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }   */
            }
        }
        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version 
                '''
            }
        } 
    }
   
}
