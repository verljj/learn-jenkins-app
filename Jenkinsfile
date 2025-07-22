pipeline {
   agent any

   environment {
      NETLIFY_SITE_ID = '62e8bad1-a86a-41f3-8400-e7a430f82bcb'
      NETLIFY_AUTH_TOKEN = credentials('netlify-token')
   }

   stages {
   /*
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
               npm install
               npm --version
               npm ci
               npm run build
               ls -la
            '''
         }
      }
      */
      stage('Tests') {
         parallel {
            stage('Unit Tests') {
               agent {
                  docker {
                     image 'node:18-alpine'
                     reuseNode true
                  }
               }
               steps {
                  sh '''
                     echo "Test Stage"
                     test -f build/index.html
                     npm test
                  '''
               }
               post {
                  always {
                     junit 'jtest-results/junit.xml'
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
                     sleep 10
                     npx playwright test --reporter=html
                  '''
               }
               post {
                  always {
                     publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
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

                  npm install netlify-cli@20.1.1
                  node_modules/.bin/netlify --version
                  echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                  node_modules/.bin/netlify status


                   '''
               }
            }

   }
}