pipeline {
    agent any

    stages {
        stage('Without docker') {
            steps {
               sh 'echo "No docker"'
            }
        }
        stage('with docker') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
               sh "echo 'docker'"
               sh "npm --version"
            }
        }        
    }
}
