pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = 'docker.io/amrabunemr98'
        DOCKER_IMAGE = 'test'
        imageTagApp "build-${BUILD_NUMBER}-app"
        imageNameapp = "${DOCKER_REGISTRY}:${imageTagApp}"
    }
        stages {
        stage('Build Docker image for app.py and push it to docker hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'DOCKER_REGISTRY_USERNAME', passwordVariable: 'DOCKER_REGISTRY_PASSWORD')]) {
                    // Authenticate with Docker Hub to push Docker image
                    sh "echo \${DOCKER_REGISTRY_PASSWORD} | docker login -u \${DOCKER_REGISTRY_USERNAME} --password-stdin"
                    
                    // Build Docker image for app.py
                    sh "docker build -t ${imageNameapp} ."

                    sh "docker tag ${imageNameapp} ${DOCKER_REGISTRY}:${imageTagApp}"
                    
                    // Push the app Docker image to Docker Hub
                    sh "docker push ${DOCKER_REGISTRY}:${imageTagApp}"
                    
                }
            }
        }
        }
}



