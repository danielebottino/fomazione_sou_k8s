pipeline {
    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        disableConcurrentBuilds()
    }

    agent any

    environment {
        APP_NAME = 'danielebottino/flask-app-example' 

        DOCKER_CREDS_ID = 'docker-hub-creds'

        DOCKER_REGISTRY_URL = ''
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checkout del codice sorgente..."
            }
        }

        stage('Determine Docker Tag') {
            steps {
                script {
                    if (env.BUILD_TAG_NAME) {
                        env.DOCKER_TAG = env.BUILD_TAG_NAME
                        echo "Sorgente: Git Tag '${env.BUILD_TAG_NAME}'. Docker Tag: '${env.DOCKER_TAG}'"
                    } 
                    else if (env.BRANCH_NAME == 'master' || env.BRANCH_NAME == 'main') {
                        env.DOCKER_TAG = 'latest'
                        echo "Sorgente: Branch master/main. Docker Tag: 'latest'"
                    } 
                    else if (env.BRANCH_NAME == 'develop') {
                        env.DOCKER_TAG = "develop-${env.GIT_COMMIT_SHORT}"
                        echo "Sorgente: Branch develop. Docker Tag: '${env.DOCKER_TAG}'"
                    } 
                    else {
                        env.DOCKER_TAG = `${env.BRANCH_NAME}-${env.GIT_COMMIT_SHORT}`
                        echo "Sorgente: Branch '${env.BRANCH_NAME}'. Docker Tag: '${env.DOCKER_TAG}'"
                    }
                    echo "Variabili rilevate:"
                    echo "  BUILD_TAG_NAME: ${env.BUILD_TAG_NAME}"
                    echo "  BRANCH_NAME: ${env.BRANCH_NAME}"
                    echo "  GIT_COMMIT_SHORT: ${env.GIT_COMMIT_SHORT}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImg = docker.build("${APP_NAME}:${env.DOCKER_TAG}")
                    echo "Immagine '${APP_NAME}:${env.DOCKER_TAG}' buildata con successo."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    def registryCreds = credentials(DOCKER_CREDS_ID)
                    
                    docker.withRegistry(DOCKER_REGISTRY_URL, DOCKER_CREDS_ID) {
                        dockerImg.push(env.DOCKER_TAG)
                    }
                    echo "Immagine '${APP_NAME}:${env.DOCKER_TAG}' spinta con successo su ${DOCKER_REGISTRY_URL}."
                }
            }
        }
    }

    post {
        failure {
            echo "Oops! La pipeline è fallita."
        }
        success {
            echo "Pipeline completata con successo!"
        }
    }
}