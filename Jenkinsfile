pipeline {
    agent any

    environment {
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
                    // Correction: Using --password-stdin to resolve EOF and security warnings
                    sh 'echo "${PASS}" | docker login -u ${USER} --password-stdin'
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
                        sh "sed -i 's|image:.*|image: ${DOCKER_IMAGE}|g' deployment.yaml"
                        
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
