pipeline {
    agent any
    
    environment {
        REPO_URL = 'https://github.com/DrawsCode/Microservice.git'
        BRANCH_NAME = 'main' // Change if needed
        DOCKER_IMAGE = 'my-microservice'
        DOCKER_TAG = 'latest'
        K8S_DEPLOYMENT = 'microservice-deployment'
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                git branch: BRANCH_NAME, url: REPO_URL
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }
        
        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                sh "kubectl set image deployment/${K8S_DEPLOYMENT} app=${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }
    }
    
    post {
        success {
            echo 'Build and deployment successful! ✅'
        }
        failure {
            echo 'Build failed! ❌ Check logs.'
        }
    }
}
