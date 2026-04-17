pipeline {
    agent any

    args {
        // Nome Immagine Docker
        IMAGE_NAME = "daniele/flask-app-example-build"

        // URL del Registry di Dockerhub o Locale http://127.0.0.1:5000/v2/...
        REGISTRY_URL = "https://index.docker.io/v1/"
    }

    stages {

        stage('Prepare Build TAG') {
            steps {
                script {
                    // Determina il tag in base alla build
                    if (args.GIT_TAG) {
                        // Se parte da una tag di Git
                        BUILD_TAG = args.GIT_TAG
                    } else if (args.GIT_BRANCH == "origin/main") {
                        // Se parte da un branch main
                        BUILD_TAG = "latest"
                    } else (args.GIT_BRANCH == "origin/develop") {
                        // Per tutto il resto dei branch
                        BUILD_TAG = "build-${args.GIT_COMMIT.take(7)}"
                    }
                    echo "Build tag generato: ${BUILD_TAG}"
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    def buildAndPushTag(Map args) {
                        def defaults = [
                            dockerfileDir: "./",
                            dockerfileName: "Dockerfile",
                            buildArgs: "",
                            pushLatest: true
                        ]
                        args = defaults + args
                        docker.withRegistry(args.REGISTRY_URL) {
                            def image = docker.build(args.IMAGE_NAME, "${args.buidArgs} ${args.dockerfileDir} -f ${args.dockerfileName}")
                            image.push(args.BUILD_TAG)
                            if(args.pushLatest) {
                                image.push("latest")
                                sh "docker rmi --force ${args.IMAGE_NAME}:latest"
                            }
                            sh "docker rmi --force ${args.IMAGE_NAME}:${args.BUILD_TAG}"

                            return "${args.IMAGE_NAME}:${args.BUILD_TAG}"
                        }
                    }

                }
            }
        }
    }
}