pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID ='0259fc4f-727e-46ed-8d5c-77d0fcdd7d55'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }
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
                            echo 'Small Change'
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
                stage('E2E') {
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
                }   
            }
        }
        stage('Deploy Stageing') {
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
                    echo "Deploying to stging. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                     node_modules/.bin/netlify deploy  --dir=build
                '''
            }
        }
        stage('Approval') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    input message: 'Ready to deploy?', ok: 'Yes, I am sure I want to deploy'
                }       
            }
        }        
        stage('Deploy Prod') {
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
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                     node_modules/.bin/netlify deploy  --dir=build --prod
                '''
            }
        }
         stage('Prod E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            environment {
                CI_ENVIRONMENT_URL = 'https://lucky-panda-0c912e.netlify.app'
            }
            steps {
                sh '''
                    
                    npx playwright test --reporter=html
                    '''
            }
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Prod HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                }
            }
        }    
    }  
}
