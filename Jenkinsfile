
pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'
        DOCKERHUB_USERNAME = 'akshaygouda646'
        BACKEND_IMAGE = "${DOCKERHUB_USERNAME}/employee_backend:v1"
        FRONTEND_IMAGE = "${DOCKERHUB_USERNAME}/employee_frontend:v1"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/akshaygouda1407/Employee_management_system.git'
            }
        }

        stage('Build Backend') {
            steps {
                dir('ems-backend') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('ems-frontend') {
                    sh 'npm ci'
                    sh 'npm run build'
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $BACKEND_IMAGE ./ems-backend'
                sh 'docker build -t $FRONTEND_IMAGE ./ems-frontend'
            }
        }

        stage('Docker Hub Login & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh 'docker push $BACKEND_IMAGE'
                    sh 'docker push $FRONTEND_IMAGE'
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}

