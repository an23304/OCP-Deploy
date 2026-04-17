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
                sudo podman build -t ${REGISTRY}/${IMAGE_NAME}:latest .
                """
            }
        }

        stage('Push Image') {
            steps {
                sh """
                sudo podman push ${REGISTRY}/${IMAGE_NAME}:latest
                """
            }
        }

        stage('Update GitOps Manifest') {
            steps {
                sh """
                rm -rf gitops
                git clone ${GITOPS_REPO} gitops
                cd gitops
                sed -i "s|${REGISTRY}/${IMAGE_NAME}:.*|${REGISTRY}/${IMAGE_NAME}:latest|g" deployment.yaml
                git config --global user.email "jenkins@example.com"
                git config --global user.name "jenkins"
                git add deployment.yaml || true
                git commit -m "Ensure manifest points to latest image" || true
                git push origin main || true
                """
            }
        }
    }
}

