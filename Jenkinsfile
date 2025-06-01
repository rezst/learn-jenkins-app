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
            '''
            }
        }    
    }
    post {
        always {
            junit 'jest-results/junit.xml'
        }
    }
}
