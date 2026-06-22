pipeline {
    agent any

    tools {
        nodejs 'Node26'
    }

    environment {
        // Docker Hub username + image name
        DOCKER_IMAGE = 'bisma08/jenkins-demo'

        // Auto increment build number tag
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'
            }
        }

        stage('Build Application') {
            steps {
                echo 'Building Node.js app...'
                sh 'npm run build'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker Image...'

                sh 'docker build -t $DOCKER_IMAGE:$IMAGE_TAG .'

                sh 'docker tag $DOCKER_IMAGE:$IMAGE_TAG $DOCKER_IMAGE:latest'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing Docker Image to Docker Hub...'

                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh 'docker login -u $DOCKER_USER -p $DOCKER_PASS'

                    sh 'docker push $DOCKER_IMAGE:$IMAGE_TAG'

                    sh 'docker push $DOCKER_IMAGE:latest'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying container...'

                sh 'docker stop jenkins-demo || true'
                sh 'docker rm jenkins-demo || true'

                sh 'docker pull $DOCKER_IMAGE:latest'

                sh 'docker run -d -p 3000:3000 --name jenkins-demo $DOCKER_IMAGE:latest'

                echo 'Application is live at http://localhost:3000'
            }
        }
    }

    post {
        success {
            echo '✅ CI/CD Pipeline executed successfully!'
        }

        failure {
            echo '❌ Pipeline failed. Check logs!'
        }
    }
}
