pipeline {
    agent any
    environment {
        DOCKERHUB_USER = 'vijayvl632'
        IMAGE_NAME = 'finacplus-app'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/vijayvlece/FinacPlus-Project.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh "docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}"
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh "kubectl apply -f k8s/deployment.yaml"
                sh "kubectl apply -f k8s/service.yaml"
                sh "kubectl set image deployment/finacplus-app finacplus-app=${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}"
            }
        }
    }
}