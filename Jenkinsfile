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
                    npm run build
                    npm ci
                    ls -la
                '''
            }
        }

        stage('Tests') {
            parallel {
                stage('Unit tests') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            npm test
                        '''
                    }
                }

                
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
                    ./node_modules/.bin/netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    ./node_modules/.bin/netlify status
                    ./node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
    }
}