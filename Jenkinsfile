pipeline {
    agent any

    environment {
        IMAGE_NAME = "myapp"
        DOCKER_CREDENTIALS = credentials('dockerhub-credentials')
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/your-repo/myapp.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Test (Quality Gate)') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t $IMAGE_NAME:${env.BUILD_NUMBER} ."
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    sh """
                    echo $DOCKER_CREDENTIALS_PSW | docker login -u $DOCKER_CREDENTIALS_USR --password-stdin
                    docker push $IMAGE_NAME:${env.BUILD_NUMBER}
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                kubectl set image deployment/myapp myapp=$IMAGE_NAME:${env.BUILD_NUMBER}
                """
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
