pipeline {
    agent any
 
    environment {
        // عدل اسم الـ Image هنا ليكون مطابق لحسابك
        IMAGE_NAME = "yusef14/my-node-app"
        TAG = "${BUILD_NUMBER}"
    }
 
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/YUSEF-RAMY/task_devops1.git'
            }
        }
 
        stage('Docker Build') {
            steps {
                // استخدام الـ variables المعرفة فوق
                sh "docker build -t ${IMAGE_NAME}:${TAG} ."
                sh "docker tag ${IMAGE_NAME}:${TAG} ${IMAGE_NAME}:latest"
            }
        }
 
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
            credentialsId: 'my-dockerhub-credentials',
            usernameVariable: 'USER',
            passwordVariable: 'PASS'
                )]) {
                    // استخدام السينجل كوت ' يمنع الـ Warning ويحمي الباسورد
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh "docker push yusef14/my-node-app:${TAG}"
                    sh "docker push yusef14/my-node-app:latest"
                }
            }
        }
 
        stage('Deploy to K8s') {
            steps {
                withCredentials([file(credentialsId: 'k8s-config', variable: 'KUBECONFIG')]) {
                    sh 'kubectl set image deployment/node-app node-app=yusef14/my-node-app:${TAG}'
                }
            }
        }
    }
    
    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Pipeline failed. Check logs.'
        }
    }
}