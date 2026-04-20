pipeline {

    agent any

    environment {
        REGISTRY_URL = "https://index.docker.io/v2/"
        IMAGE_NAME   = "danielebottino/flask-app-example-build"     // cambia con il tuo DockerHub username
    }

    stages {    

        stage('Prepare Build Tag') {
            steps {
                script {
                    if (env.TAG_NAME) {
                        // Build da tag Git
                        def BUILD_TAG = env.TAG_NAME
                    } else if (BRANCH_NAME == "main") {
                        def BUILD_TAG = "latest"
                    } else if (BRANCH_NAME == "develop") {
                        def BUILD_TAG = "develop-${env.GIT_COMMIT}"
                    } else {
                        def BUILD_TAG = "build-${env.GIT_COMMIT}"
                    }
                    echo "Docker tag generato: ${BUILD_TAG}"
                }
            }
        }

        stage('Build Docker Image & Push') {
            steps {
                script {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
                                  usernameVariable: 'DOCKER_USER',
                                  passwordVariable: 'DOCKER_TOKEN')]) {
                                sh """
                                    docker build -t danielebottino/flask-app-example-build .
                                    echo \$DOCKER_TOKEN | docker login -u \$DOCKER_USER --password-stdin
                                    docker push ${IMAGE_NAME}:${BUILD_TAG}
                                """
                                    }
                }
            }
        }

        stage('Cleanup') {
            steps {
                script {
                    sh "docker rmi ${IMAGE_NAME}:${BUILD_TAG} || true"
                }
            }
        }
    }

    post {
        success {
            echo "Build completata con successo: ${IMAGE_NAME}:${BUILD_TAG}"
        }
        failure {
            echo "Build fallita"
        }
    }
}
