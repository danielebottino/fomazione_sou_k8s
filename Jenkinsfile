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
                    def branch = env.GIT_BRANCH ?: sh(script: "git rev-parse --abbrev-ref HEAD", returnStdout: true).trim()
                    def sha = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()

                    if (env.GIT_TAG_NAME) {
                        // Build da tag Git
                        BUILD_TAG = env.GIT_TAG_NAME
                    } else if (branch == "main") {
                        BUILD_TAG = "latest"
                    } else if (branch == "develop") {
                        BUILD_TAG = "develop-${sha}"
                    } else {
                        BUILD_TAG = "build-${sha}"
                    }

                    echo "Docker tag generato: ${BUILD_TAG}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        docker build -t ${IMAGE_NAME}:${BUILD_TAG} .
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry(REGISTRY_URL, 'dockerhub-credentials') {
                        sh "docker push ${IMAGE_NAME}:${BUILD_TAG}"
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
