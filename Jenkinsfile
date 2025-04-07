pipeline {
    agent any

    environment {
      NETLIFY_SITE_ID = 'e2e2ee40-6d01-4832-ab6f-a898e61dd0d4'
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

        stage('Test') {
         agent {
            docker {
               image 'node:18-alpine'
               reuseNode true
            }
         }
         steps {
            sh '''
               test -f build/index.html
               npm test
            '''
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
                  echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                  node_modules/.bin/netlify status
                  node_modules/.bin/netlify deploy --dir=build --prod

                  '''
            }
        }
    }

      post {
         always {
               junit 'test-results/junit.xml'
         }
         success {
               echo 'Build and tests completed successfully!'
         }
         failure {
               echo 'Build or tests failed.'
         }
      }
}
