pipeline {
    agent any

    environment {
        REGION = 'ap-northeast-2'
        ECR_PATH = '621917999036.dkr.ecr.ap-northeast-2.amazonaws.com'
        ECR_IMAGE = 'web_jenkins'
        AWS_CREDENTIAL_ID = 'AWS'
        EKS_API = 'https://D2A59EBDDDD7B486CF3AF9A480165D0D.gr7.ap-northeast-2.eks.amazonaws.com'
        EKS_CLUSTER_NAME = 'myeks'
        EKS_JENKINS_CREDENTIAL_ID = 'kubernetes-deploy'
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
                sh """
                    docker rmi ${ECR_PATH}/${ECR_IMAGE}:v${env.BUILD_NUMBER}
                    docker rmi ${ECR_PATH}/${ECR_IMAGE}:latest
                """
            }
        }

        stage('Deploy to k8s'){
        withKubeConfig([credentialsId: "{EKS_JENKINS_CREDENTIAL_ID}",
                        serverUrl: "${EKS_API}",
                        clusterName: "${EKS_CLUSTER_NAME}"]){
            sh "sed 's/IMAGE_VERSION/v${env.BUILD_ID}/g' service.yaml > output.yaml"
            sh "aws eks --region ${REGION} update-kubeconfig --name ${EKS_CLUSTER_NAME}"
            sh "kubectl apply -f output.yaml"
            sh "rm output.yaml"
             }
        }
    }
}
