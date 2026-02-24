pipeline {
    agent any

    environment {
        DOCKER_USER = "aksarsr"              // 🔁 change to your Docker Hub username
        BACKEND_IMAGE = "dd-backend"
        FRONTEND_IMAGE = "dd-frontend"
        APP_DIR = "/home/ubuntu/task-app-ci-cd"  // 🔁 path on VM after clone
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/Aksar-srk/task-app-ci-cd.git'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER_NAME',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER_NAME --password-stdin'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker build -t $DOCKER_USER/$BACKEND_IMAGE ./backend'
                sh 'docker build -t $DOCKER_USER/$FRONTEND_IMAGE ./frontend'
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                sh 'docker push $DOCKER_USER/$BACKEND_IMAGE'
                sh 'docker push $DOCKER_USER/$FRONTEND_IMAGE'
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                cd $APP_DIR
                docker-compose down
                docker-compose pull
                docker-compose up -d
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment Successful!'
        }
        failure {
            echo '❌ Deployment Failed. Check logs.'
        }
    }
}
