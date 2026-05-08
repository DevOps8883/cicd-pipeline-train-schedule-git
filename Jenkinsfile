pipeline {
    agent any

    environment {
        // Tagging image with build number for unique versioning
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
                    script {
                        // Retry block handles intermittent EOF handshake failures
                        retry(5) {
                            sh 'echo "${PASS}" | docker login -u ${USER} --password-stdin'
                            sh "docker push ${DOCKER_IMAGE}"
                        }
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubernetes-config', variable: 'KUBE_PATH')]) {
                    script {
                        // Update manifest with new image version
                        // Note: Using g flag to ensure all instances are updated
                        sh "sed -i 's|image:.*|image: ${DOCKER_IMAGE}|g' deployment.yaml"
                        
                        // Clear proxies to ensure kubectl can reach the local Minikube IP
                        sh """
                            export http_proxy=
                            export https_proxy=
                            export no_proxy=
                            kubectl --kubeconfig=\$KUBE_PATH apply -f deployment.yaml --validate=false
                        """
                    }
                }
            }
        }
    }
}
