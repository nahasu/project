pipeline {
    agent any

    environment{
        REGION = 'ap-northeast-2'
        ECR_PATH = {ECR로 가서 Repository의 URI을 가져오되 /repository-name 은 제거}
        ECR_IMAGE = 'ecr 이미지 이름'
        AWS_CREDENTIAL_ID = 'Jenkins에서 설정한 AWS Credential'

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
