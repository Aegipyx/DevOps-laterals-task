pipeline {
    agent any

    environment {
        FRONTEND_IMAGE = 'aegipyx/login-app-frontend:latest'
        BACKEND_IMAGE  = 'aegipyx/login-app-backend:latest'
        DOCKERHUB_CREDS = credentials('dockerhub-creds')
    }

    triggers {
        pollSCM('H/5 * * * *')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Images'){
            steps {
                sh '''
                  docker build -t $FRONTEND_IMAGE ./Frontend
                  docker build -t $BACKEND_IMAGE ./Backend
                '''
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                  echo $DOCKERHUB_CREDS_PSW | docker login -u $DOCKERHUB_CREDS_USR --password-stdin
                  docker push $FRONTEND_IMAGE
                  docker push $BACKEND_IMAGE
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                  docker-compose down
                  docker-compose pull
                  docker-compose up -d
                '''
            }
        }
    }
}