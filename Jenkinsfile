pipeline{
    agent any

    triggers {
        pollSCM('H/5 * * * *')
    }

    environment{
        FRONTEND_IMAGE = 'aegipyx/login-app-frontend'
        BACKEND_IMAGE = 'aegipyx/login-app-backend'
        DOCKER_CREDS = 'dockerhub-creds'
    }

    stages{
        stage('Checkout'){
            steps{
                checkout scm
            }
        }

        stage('Build images'){
            steps{
                sh '''
                docker build -t $FRONTEND_IMAGE ./Frontend
                docker build -t $BACKEND_IMAGE ./Backend
                '''
            }
        }

        stage('Push images'){
            steps{
                withCredentials([usernamePassword(
                    credentialsId: DOCKER_CREDS, 
                    passwordVariable: 'DOCKER_PASS', 
                    usernameVariable: 'DOCKER_USER'
                )]){
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $FRONTEND_IMAGE
                    docker push $BACKEND_IMAGE
                    '''
                }
            }
        }

        stage('Deploy'){
            steps{
                sh 'docker compose down'
                sh 'docker compose pull'
                sh 'docker compose up -d'
            }
        }
    }
}