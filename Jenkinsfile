pipeline {
    agent any

    environment {
        REGION = 'ap-northeast-2'
        ECR_PATH = '621917999036.dkr.ecr.ap-northeast-2.amazonaws.com'
        ECR_IMAGE = 'web_jenkins'
        AWS_CREDENTIAL_ID = 'AWS'
        oldImageTag = "${env.BUILD_NUMBER.toInteger() - 2}"
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
                    // Docker 이미지 빌드
                    docker.build("${ECR_PATH}/${ECR_IMAGE}:v${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    // ECR에 이미지 푸시
                    docker.withRegistry("https://${ECR_PATH}", "ecr:${REGION}:${AWS_CREDENTIAL_ID}") {
                        docker.image("${ECR_PATH}/${ECR_IMAGE}:v${env.BUILD_NUMBER}").push()
                    }
                }
            }
        }

        stage('CleanUp Images') {
            steps {
                // 사용하지 않는 Docker 이미지 정리
                if (${oldImageTag} > 0){
                     def oldImage = "${ECR_PATH}/${ECR_IMAGE}:v${oldImageTag}"
                    sh "docker rmi ${oldImage}"
                }
            }
        }
    }
}



