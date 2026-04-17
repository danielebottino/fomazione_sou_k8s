pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                sh "docker build -t daniele/flask-app ."
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push daniele/flask-app"
            }
        }
    }
}
