pipeline {

    agent any

    environment {
        REGISTRY_URL = "https://index.docker.io/v2/"
        IMAGE_NAME   = "danielebottino/flask-app-example-build"     // cambia con il tuo DockerHub username
        BUILD_TAG = ''
    }

    stages {    

        stage('Prepare Build Tag') {
            steps {
                script {
                    if (env.TAG_NAME) {
                        env.BUILD_TAG = env.TAG_NAME
                    } else if (env.BRANCH_NAME == 'main') {
                        env.BUILD_TAG = 'latest'
                    } else if (env.BRANCH_NAME == 'develop') {
                        env.BUILD_TAG = "develop-${env.GIT_COMMIT?.take(7)}"
                    } else {
                        env.BUILD_TAG = "build-${env.GIT_COMMIT?.take(7)}"
                    }
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
                                    docker build -t danielebottino/flask-app-example-build:${env.BUILD_TAG} .
                                    echo \$DOCKER_TOKEN | docker login -u \$DOCKER_USER --password-stdin
                                    docker push ${env.IMAGE_NAME}:${env.BUILD_TAG}
                                """
                                    }
                }
            }
        }
    }

    post {
        success {
            echo "Build completata con successo: ${env.IMAGE_NAME}:${env.BUILD_TAG}"
        }
        failure {
            echo "Build fallita"
        }
    }
}
