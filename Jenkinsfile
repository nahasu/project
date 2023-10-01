pipeline {
    agent any

    environment{
        REGION = 'ap-northeast-2'
        ECR_PATH = 'https://621917999036.dkr.ecr.ap-northeast-2.amazonaws.com'
        ECR_IMAGE = 'web_jenkins'
        AWS_CREDENTIAL_ID = 'AWS'

    }
    stages {
        stage('Clone Repository'){
            checkout scm
        }
        stage('Docker Build'){
        docker.withRegistry("https://${ECR_PATH}", "ecr:${REGION}:${AWS_CREDENTIAL_ID}"){
            image = docker.build("${ECR_PATH}/${ECR_IMAGE}")
            }
        }
        stage('Push to ECR'){
            docker.withRegistry("https://{ECR_PATH}", "ecr:${REGION}:${AWS_CREDENTIAL_ID}"){
                image.push("v${env.BUILD_NUMBER}")
            }
        }
        stage('CleanUp Images'){
            sh"""
            docker rmi ${ECR_PATH}/${ECR_IMAGE}:v$BUILD_NUMBER
            docker rmi ${ECR_PATH}/${ECR_IMAGE}:latest
            """
        }
    }
}
