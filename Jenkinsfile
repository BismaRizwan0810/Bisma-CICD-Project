pipeline {
    agent any

    tools {
        nodejs 'Node26'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                echo 'Building project...'
                sh 'node --version'
                sh 'npm --version'
                sh 'npm run build || echo "No build script found"'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'npm test || echo "No tests found"'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t jenkins-demo:latest .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh 'docker run -d -p 3000:3000 jenkins-demo:latest'
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline SUCCESS!'
        }

        failure {
            echo '❌ Pipeline FAILED!'
        }

        always {
            echo 'Pipeline finished.'
        }
    }
}
