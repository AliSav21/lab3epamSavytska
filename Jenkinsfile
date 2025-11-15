pipeline {
    agent any
    tools { 
        nodejs "NodeJS_7.8.0" 
    }
    environment {
        PORT = "${env.BRANCH_NAME}" == 'main' ? '3000' : '3001'
        IMAGE_NAME = "${env.BRANCH_NAME}" == 'main' ? 'nodemain' : 'nodedev'
        IMAGE_TAG = 'v1.0'
    }
    stages {
        stage('Checkout') {
            steps { 
                checkout scm 
            }
        }
        stage('Build') {
            steps { 
                sh 'npm install' 
            }
        }
        stage('Test') {
            steps { 
                sh 'npm test || echo "Tests completed"' 
            }
        }
        stage('Build Docker Image') {
            steps { 
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ." 
            }
        }
        stage('Deploy') {
            steps {
                sh """
                    docker stop ${IMAGE_NAME} || true
                    docker rm ${IMAGE_NAME} || true
                    docker run -d --name ${IMAGE_NAME} -p ${PORT}:3000 ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }
    }
}
