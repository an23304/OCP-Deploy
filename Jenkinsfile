pipeline {
    agent any

    environment {
        REGISTRY = "mirror-registry.test.vn:8443"
        IMAGE_NAME = "jenkins-lab"
        GITOPS_REPO = "https://github.com/an23304/OCP-Deploy.git"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                sh """
                sudo podman build -t ${REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER} .
                """
            }
        }

        stage('Push Image') {
            steps {
                sh """
                sudo podman push ${REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}
                """
            }
        }

        stage('Update GitOps Manifest') {
            steps {
                sh """
                git clone ${GITOPS_REPO} gitops
                cd gitops/k8s
                sed -i "s|${REGISTRY}/${IMAGE_NAME}:.*|${REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}|g" deployment.yaml
                git config --global user.email "jenkins@example.com"
                git config --global user.name "jenkins"
                git add deployment.yaml
                git commit -m "Update image to build ${BUILD_NUMBER}"
                git push origin main
                """
            }
        }
    }
}

