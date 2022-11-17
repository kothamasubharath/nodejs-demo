pipeline{
    agent any
    environment{
        AWS_ACCOUNT_ID="838342381657"
        AWS_DEFAULT_REGION="ap-south-1"
        IMAGE_REPO_NAME="deploy-dev"
        IMAGE_TAG="latest"
        REPOSITORY_URL = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }
    stages{
        stage('git fetch'){
            steps{
                git 'https://github.com/kothamasubharath/nodejs-demo.git'
            }
        }
        stage('build image'){
            steps{
                script{
                    sh 'docker system prune -a'
                    dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                } 
            }  
        }
        stage('Logging to AWS ECR'){
            steps{
                script{
                       sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 838342381657.dkr.ecr.us-east-1.amazonaws.com/${IMAGE_REPO_NAME}"
                }
            }     
        }
        stage('pushing to ECR'){
            steps{
                script{
                    sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URL}:${IMAGE_TAG}"
                    sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }
        stage('deploying app to kubernetes'){
            steps{
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kube', namespace: '', serverUrl: '') {
                    sh 'kubectl apply -f deployment1.yml'
                }
            }
        }
    }
}

