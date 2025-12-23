pipeline {
    agent any
    environment {
        IMAGE_NAME  = "python-demo-app"
        IMAGE_TAG   = "latest"
        DEPLOY_USER = "ec2-user"
        DEPLOY_HOST = "DEMO_SERVER_PUBLIC_IP"
        APP_PORT    = "5000"
    }
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Test') {
            steps {
                sh '''
                pip3 install -r requirements.txt
                pytest
                '''
            }
        }
        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh $DEPLOY_USER@$DEPLOY_HOST "
                docker stop $IMAGE_NAME || true &&
                docker rm $IMAGE_NAME || true &&
                docker run -d --name $IMAGE_NAME -p $APP_PORT:5000 $IMAGE_NAME:$IMAGE_TAG
                "
                '''
            }
        }
    }
}
