pipeline {
    agent any

    environment {
        DOCKER_USER = "yourdockerhub"
        BACKEND_IMAGE = "mean-backend"
        FRONTEND_IMAGE = "mean-frontend"
        COMPOSE_DIR = "/home/ubuntu/discover-dollar-mean-devops"
    }

    stages {

        stage('Clone Repo') {
            steps {
                git 'https://github.com/<your-username>/discover-dollar-mean-devops.git'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Build Images') {
            steps {
                sh 'docker build -t $DOCKER_USER/$BACKEND_IMAGE ./backend'
                sh 'docker build -t $DOCKER_USER/$FRONTEND_IMAGE ./frontend'
            }
        }

        stage('Push Images') {
            steps {
                sh 'docker push $DOCKER_USER/$BACKEND_IMAGE'
                sh 'docker push $DOCKER_USER/$FRONTEND_IMAGE'
            }
        }

        stage('Deploy Latest Containers') {
            steps {
                sh '''
                cd $COMPOSE_DIR
                docker-compose down
                docker-compose pull
                docker-compose up -d
                '''
            }
        }
    }
}