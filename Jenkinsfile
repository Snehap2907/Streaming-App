pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID = '356042515069'
        AWS_DEFAULT_REGION = 'us-east-1'
        ECR_FRONTEND_REPO = "\"356042515069.dkr.ecr.us-east-1.amazonaws.com/streaming-frontend"
        ECR_BACKEND_REPO  = "\"356042515069.dkr.ecr.us-east-1.amazonaws.com/streaming-backend"
"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Snehap2907/Streaming-App'
            }
        }
        stage('ECR Login') {
            steps {
                sh 'aws ecr get-login-password --region \({AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin\){AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com'
            }
        }
        stage('Build & Push Frontend') {
            steps {
                dir('frontend') {
                    sh 'docker build -t \({ECR_FRONTEND_REPO}:\){IMAGE_TAG} -t ${ECR_FRONTEND_REPO}:latest .'
                    sh 'docker push \({ECR_FRONTEND_REPO}:\){IMAGE_TAG}'
                    sh 'docker push ${ECR_FRONTEND_REPO}:latest'
                }
            }
        }
        stage('Build & Push Backend') {
            steps {
                dir('backend') {
                    sh 'docker build -t \({ECR_BACKEND_REPO}:\){IMAGE_TAG} -t ${ECR_BACKEND_REPO}:latest .'
                    sh 'docker push \({ECR_BACKEND_REPO}:\){IMAGE_TAG}'
                    sh 'docker push ${ECR_BACKEND_REPO}:latest'
                }
            }
        }
        stage('Deploy to EKS via Helm') {
            steps {
                sh '''
                aws eks update-kubeconfig --name streaming-eks-cluster --region ${AWS_DEFAULT_REGION}
                helm upgrade --install mern-app ./helm/mern-app \
                  --set frontend.image.repository=${ECR_FRONTEND_REPO} \
                  --set frontend.image.tag=${IMAGE_TAG} \
                  --set backend.image.repository=${ECR_BACKEND_REPO} \
                  --set backend.image.tag=${IMAGE_TAG}
                '''
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}