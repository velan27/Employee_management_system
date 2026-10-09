
pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'
        DOCKERHUB_USERNAME = 'velanjs27'
        BACKEND_IMAGE = "${DOCKERHUB_USERNAME}/employee_backend:v1"
        FRONTEND_IMAGE = "${DOCKERHUB_USERNAME}/employee_frontend:v1"
        FRONTEND_API_URL = 'http://localhost:8083/api/employees'
        DOCKER_NETWORK = 'employee_management_system_ems-network'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/velan27/Employee_management_system.git'
            }
        }

        stage('Build Backend') {
            steps {
                dir('ems-backend') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

     

        stage('Docker Build') {
            steps {
                sh 'docker build -t $BACKEND_IMAGE ./ems-backend'
                sh 'docker build --build-arg VITE_API_BASE_URL=$FRONTEND_API_URL -t $FRONTEND_IMAGE ./ems-frontend'
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

        stage('Deploy Test Containers') {
            steps {
                sh '''
                    docker pull "$BACKEND_IMAGE"
                    docker pull "$FRONTEND_IMAGE"

                    docker rm -f employee-backend-jenkins-test employee-frontend-jenkins-test 2>/dev/null || true

                    docker run -d \
                      --name employee-backend-jenkins-test \
                      --network "$DOCKER_NETWORK" \
                      -p 8087:8082 \
                      "$BACKEND_IMAGE"

                    docker run -d \
                      --name employee-frontend-jenkins-test \
                      -p 8088:80 \
                      "$FRONTEND_IMAGE"
                '''
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}

