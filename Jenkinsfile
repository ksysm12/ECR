REGION = 'ap-northeast-1'
EKS_API = 'https://A82533F45379F4E1BCF558C8D5AB1493.gr7.ap-northeast-1.eks.amazonaws.com'
EKS_CLUSTER_NAME='k8s-demo'
EKS_NAMESPACE='default'
EKS_JENKINS_CREDENTIAL_ID='kubectl-deploy-credentials'
ECR_PATH = '035884387009.dkr.ecr.ap-northeast-1.amazonaws.com'
ECR_IMAGE = 'test1'
AWS_CREDENTIAL_ID = 'jenkins-aws-JHS-credentials'

node {
    stage('Clone Repository'){
        checkout scm
    }
    stage('Docker Build'){
        // Docker Build
        docker.withRegistry("https://${ECR_PATH}", "ecr:${REGION}:${AWS_CREDENTIAL_ID}"){
            image = docker.build("${ECR_PATH}/${ECR_IMAGE}", "--network=host --no-cache .")
        }
    }
    stage('Push to ECR'){
        docker.withRegistry("https://${ECR_PATH}", "ecr:${REGION}:${AWS_CREDENTIAL_ID}"){
            image.push("v${env.BUILD_NUMBER}")
        }
    }
    stage('CleanUp Images'){
        sh"""
        docker rmi ${ECR_PATH}/${ECR_IMAGE}:v$BUILD_NUMBER
        docker rmi ${ECR_PATH}/${ECR_IMAGE}:latest
        """
    }
    stage('Deploy to K8S'){
        withKubeConfig([credentialsId: "kubectl-deploy-credentials",
                        serverUrl: "${EKS_API}",
                        clusterName: "${EKS_CLUSTER_NAME}"]){
            sh "sed 's/IMAGE_VERSION/${env.BUILD_ID}/g' service.yaml > output.yaml"
            sh "aws eks --region ${REGION} update-kubeconfig --name ${EKS_CLUSTER_NAME}"
        }
    }
}
