pipeline {
    agent any

    environment {
        // Tagging each image with the build number (e.g., train-schedule:10)
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
                    retry(3) {
                        sh "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubernetes-config', variable: 'KUBE_PATH')]) {
                    script {
                        // Update the deployment file with the new image tag
                        sh "sed -i 's|image:.*|image: ${DOCKER_IMAGE}|g' deployment.yaml"
                        
                        // Fix: Unset proxy variables to prevent the Jenkins redirect error
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
