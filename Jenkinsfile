pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = "amrabunemr98"
        DOCKER_IMAGE = "test"
        imageTagApp = "build-${BUILD_NUMBER}-app"
        imageNameapp = "${DOCKER_REGISTRY}:${imageTagApp}"
        OPENSHIFT_PROJECT = 'amr'
        OPENSHIFT_SERVER = 'https://console-openshift-console.apps.ocpuat.devopsconsulting.org'


    }

    stages {
        stage('Build Docker image for app.py and push it to docker hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'Dockerhub', usernameVariable: 'DOCKER_REGISTRY_USERNAME', passwordVariable: 'DOCKER_REGISTRY_PASSWORD')]) {
                    // Authenticate with Docker Hub to push Docker image
                    sh "echo \${DOCKER_REGISTRY_PASSWORD} | docker login -u \${DOCKER_REGISTRY_USERNAME} --password-stdin"
                    
                    // Build Docker image for app.py
                    sh "docker build -t ${imageNameapp} ."

                    sh "docker tag ${imageNameapp} docker.io/${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${imageTagApp}"
                    
                    // Push the app Docker image to Docker Hub
                    sh "docker push docker.io/${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${imageTagApp}"
                    
                    // Remove the locally built app Docker image
                    sh "docker rmi docker.io/${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${imageTagApp}"
                }
            }
        }


        stage('Deploy to OpenShift') {
            steps {
                script {
                    // Read the content of the OpenShift configuration file from the secret text credential
                    withCredentials([file(credentialsId: 'OpenShiftConfig', variable: 'OPENSHIFT_CONFIG_PATH')]) {
                        def openShiftConfigContent = readFile "${OPENSHIFT_CONFIG_PATH}"
                        
                        // Log the content to verify
                        echo "OpenShift Config Content:"
                        echo "${openShiftConfigContent}"

                        // Log in to OpenShift using the configuration
                        sh "oc login --config <(echo '${openShiftConfigContent}') --insecure-skip-tls-verify"

                        sh "oc project \${OPENSHIFT_PROJECT}"

                        // Apply the deployment file
                        sh "oc apply -f deployment.yml"
                }
            }
        }
    }
}

