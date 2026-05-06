pipeline {
    agent any

    environment {
        // Keeps every build version unique in Docker Hub
        DOCKER_IMAGE = "davidadeleke23/train-schedule:${env.BUILD_NUMBER}"
    }

    stages {
        stage('Build Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh 'docker login -u ${USER} -p ${PASS}'
                    // Retry handles temporary 'EOF' or network glitches
                    retry(3) {
                        sh "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Using 'file' type credential to handle the kubeconfig manually
                withCredentials([file(credentialsId: 'kubeconfig-file', variable: 'KUBE_PATH')]) {
                    script {
                        // Dynamically updates the deployment.yaml with the new build tag
                        sh "sed -i 's|image:.*|image: ${DOCKER_IMAGE}|g' deployment.yaml"
                        
                        // --validate=false prevents kubectl from redirected to Jenkins during the OpenAPI check
                        sh "kubectl --kubeconfig=\$KUBE_PATH apply -f deployment.yaml --validate=false"
                    }
                }
            }
        }
    }
}
