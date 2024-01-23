pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = "amrabunemr98"
        DOCKER_IMAGE = "test"
        imageTagApp = "build-${BUILD_NUMBER}-app"
        imageNameapp = "${DOCKER_REGISTRY}:${imageTagApp}"
        OPENSHIFT_PROJECT = 'amr'
        // OPENSHIFT_SERVER = 'https://console-openshift-console.apps.ocpuat.devopsconsulting.org'
        OPENSHIFT_SERVER = 'https://api.ocpuat.devopsconsulting.org:6443'



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
                    withCredentials([string(credentialsId: 'OpenShiftConfig', variable: 'OPENSHIFT_SECRET')]) {
                    sh "oc login --token=\${OPENSHIFT_SECRET} \${OPENSHIFT_SERVER} --insecure-skip-tls-verify"
                    }                    // sh "export KUBECONFIG=\$KUBECONFIG_FILE"
                    sh "oc project \${OPENSHIFT_PROJECT}"
                    // Apply the deployment file
                    sh "oc apply -f deployment.yml -n ${OPENSHIFT_PROJECT}"
                
                }
            }
        }
    }
}
