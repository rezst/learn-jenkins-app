pipeline {
    agent any

environment
{
    NETLIFY_SITE_ID = "1fb0f7d8-745a-47cf-b30b-80ebaa8c8791"
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
                echo docker
                npm --version
                npm ci
                npm run build
                ls -la
                '''
            }
        }

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
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report playwright local', reportTitles: '', useWrapperFileDirectly: true])
            }
        }
        }    
       }
    }
    stage('Deploy Staging'){
    agent {
        docker {
            image 'node:18-alpine'
            reuseNode true
        }
    }
    steps {
    sh '''
    echo "THIS IS SITE ID: $NETLIFY_SITE_ID"
    node_modules/.bin/netlify status
    node_modules/.bin/netlify deploy --dir=build
    echo 'Change'
    '''
    }
    }  
    stage('Approval'){
        steps{
            options{
                timeout(time: 15, unit:"MINUTES"){
                    input message:"Ready to deploy?", ok:"Yes, I'm ready to deploy!"
                }
            }
        }
    }

    stage('Deploy Prod'){
        agent {
            docker {
                image 'node:18-alpine'
                reuseNode true
            }
        }
        steps {
        sh '''npx npm-check-updates -u
        npm install netlify-cli@20.1.1
        echo "THIS IS SITE ID: $NETLIFY_SITE_ID"
        node_modules/.bin/netlify status
        node_modules/.bin/netlify deploy --dir=build --prod
        echo 'Change'
        '''
        }
    }  
    stage('Prod E2E'){
    agent {
        docker {
            image 'mcr.microsoft.com/playwright:v1.52.0-noble'
            reuseNode true
        }
    }

    environment{
        CI_ENVIRONMENT_URL = 'https://elaborate-centaur-88757f.netlify.app'
    }

    steps{
    sh'''
        npx playwright test --reporter=html
    '''
    }
    post {
        always {
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report playwright E2E', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
    }    
}
}

