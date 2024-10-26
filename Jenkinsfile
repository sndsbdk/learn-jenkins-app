pipeline {
    agent any
    environment {
        NETLIFY_SITE_ID = '4795a911-cae5-4868-bba1-65d2962f4f7e'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }
    stages {
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
        stage('Tests') {
            parallel {
                stage('Unit tests') {
                    steps {
                        script {
                            docker.image('node:18-alpine').inside {
                                sh 'npm test'
                            }
                        }
                    }
                }
                stage('E2E') {
                    steps {
                        script {
                            docker.image('mcr.microsoft.com/playwright:v1.39.0-jammy').inside {
                                sh '''
                                    npm install serve
                                    node_modules/.bin/serve -s build &
                                    sleep 10
                                    npx playwright test --reporter=html
                                '''
                            }
                        }
                    }
                }
            }
        }
        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                }
            }

            steps {
                sh '''
                    npm install netlify-cli
                    ./node_modules/.bin/netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    ./node_modules/.bin/netlify status
                    ./node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
    }
}
