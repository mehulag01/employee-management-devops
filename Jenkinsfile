pipeline {
    agent any

    options {
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Backend Tests') {
            steps {
                bat 'docker run --rm -v "%WORKSPACE%/backend:/src" -w /src golang:1.22-alpine go test ./...'
            }
        }

        stage('Build Backend Image') {
            steps {
                bat 'docker build -t employee-backend:%BUILD_NUMBER% backend'
            }
        }

        stage('Build Frontend Image') {
            steps {
                bat 'docker build -t employee-frontend:%BUILD_NUMBER% -f frontend/Dockerfile frontend'
            }
        }
    }
}
