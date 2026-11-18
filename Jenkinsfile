
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

        stage('Push Images to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKERHUB_USERNAME',
                    passwordVariable: 'DOCKERHUB_TOKEN'
                )]) {
                    bat 'echo %DOCKERHUB_TOKEN%| docker login -u %DOCKERHUB_USERNAME% --password-stdin'

                    bat 'docker tag employee-backend:%BUILD_NUMBER% mehulag/employee-backend:%BUILD_NUMBER%'
                    bat 'docker tag employee-backend:%BUILD_NUMBER% mehulag/employee-backend:latest'

                    bat 'docker tag employee-frontend:%BUILD_NUMBER% mehulag/employee-frontend:%BUILD_NUMBER%'
                    bat 'docker tag employee-frontend:%BUILD_NUMBER% mehulag/employee-frontend:latest'

                    bat 'docker push mehulag/employee-backend:%BUILD_NUMBER%'
                    bat 'docker push mehulag/employee-backend:latest'

                    bat 'docker push mehulag/employee-frontend:%BUILD_NUMBER%'
                    bat 'docker push mehulag/employee-frontend:latest'

                    bat 'docker logout'
                }
            }
        }
    }
}
