pipeline {
    agent any
    environment {
        NETLIFY_SITE_ID = '4795a911-cae5-4868-bba1-65d2962f4f7e'
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

        stage ('Tests'){
         parallel{
          stage ('Unit tests'){
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
               sh '''
                test -f build/index.html
                npm test
               '''
            }
            post{
              always {
                junit 'test-results/junit.xml'
              }
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
              echo "deploying tp production . Site Id: $NETLIFY_SITE_ID"
              '''                
            }
        }
    }
    
   
}
