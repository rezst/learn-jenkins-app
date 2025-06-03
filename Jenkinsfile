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
            steps {
                sh '''
                ls -la
                echo docker
                npm --version
                npm ci
                npm run build
                ls -la
                '''
            }
        }
/*
    stage('Run tests'){
        parallel{
            stage('Test'){
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
            sh'''
            echo "Test stage"
            test -f build/index.html
            npm test
            '''
            }
        post {
            always {
                junit 'jest-results/junit.xml'
            }
        }
        }    
         stage('E2E'){
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.52.0-noble'
                    reuseNode true
                }
            }
            steps{
            sh'''
              npm install serve
              node_modules/.bin/serve -s build &
              sleep 15
              npx playwright install
              npx playwright test
              npx playwright test --reporter=html
            '''
            }
        post {
            always {
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report playwright', reportTitles: '', useWrapperFileDirectly: true])
            }
        }
        }    
        }
    }
    */

    stage('Run netlify'){
        agent {
            docker {
                image 'node:18-alpine'
                reuseNode true
            }
        }
        steps {
        sh "npm install netlify-cli@20.1.1"
        sh "netlify --version"
        sh "npm audit fix --force"
        }
    }
    }
}
