pipeline {
    agent any

    environment {
        REGION = 'ap-northeast-2'
        ECR_PATH = '621917999036.dkr.ecr.ap-northeast-2.amazonaws.com'
        ECR_IMAGE = 'web_jenkins'
        AWS_CREDENTIAL_ID = 'AWS'
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    docker.withRegistry('https://${ECR_PATH}', 'ecr:${AWS_CREDENTIAL_ID}') {
                        def image = docker.build("${ECR_PATH}/${ECR_IMAGE}")
                    }
                }
            }
        }
        stage('Push to ECR') {
            steps {
                script {
                    //docker.withRegistry("https://${ECR_PATH}", "ecr:${REGION}:${AWS_CREDENTIAL_ID}") {
                        image.push("v${env.BUILD_NUMBER}")
                    }
                }
            }
        }
        stage('CleanUp Images') {
            steps {
                script {
                    sh """
                        docker rmi ${ECR_PATH}/${ECR_IMAGE}:v${env.BUILD_NUMBER}
                        docker rmi ${ECR_PATH}/${ECR_IMAGE}:latest
                    """
                }
            }
        }
    }
}
