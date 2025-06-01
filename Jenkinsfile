pipeline {
    agent any

    stages {
        stage('with docker') {
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
    }
}
