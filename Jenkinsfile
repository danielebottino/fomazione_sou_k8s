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

                    if (env.TAG_NAME) {
                        // Build da tag Git
                        BUILD_TAG = env.TAG_NAME
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
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
                                  usernameVariable: 'DOCKER_USER',
                                  passwordVariable: 'DOCKER_TOKEN')]) {
                                sh """
                                    docker logout || true

                                     echo "\$DOCKER_TOKEN" | docker login https://hubproxy.docker.internal:5555 \
                                    --username "\$DOCKER_USER" --password-stdin

                                    echo "\$DOCKER_TOKEN" | docker login https://index.docker.io/v2/ \
                                    --username "\$DOCKER_USER" --password-stdin

                                    echo "\$DOCKER_TOKEN" | docker login https://registry-1.docker.io/v2/ \
                                    --username "\$DOCKER_USER" --password-stdin
                                """
                                    def customImage = docker.build("${IMAGE_NAME}:${BUILD_TAG}")
                                    customImage.push()
                                    }
                }
            }
        }
        stage('Logout from Docker') {
            steps {
                script {
                    sh """
                        docker logout || true
                    """    
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry(REGISTRY_URL, 'dockerhub-credentials') {
                        sh """
                        docker push ${IMAGE_NAME}:${BUILD_TAG}"""
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
