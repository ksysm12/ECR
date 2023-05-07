REGION = 'ap-northeast-1'
EKS_API = 'https://E09B58ED12FCA37078881B65A241DAE5.gr7.ap-northeast-1.eks.amazonaws.com'
EKS_CLUSTER_NAME='terraform-eks-demo'
EKS_NAMESPACE='default'
EKS_JENKINS_CREDENTIAL_ID='ksysm12'
ECR_PATH = '035884387009.dkr.ecr.ap-northeast-1.amazonaws.com'
ECR_IMAGE = 'test-repository'
AWS_CREDENTIAL_ID = 'aws-credentials'

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
            sh "kubectl apply -f output.yaml"
            sh "rm output.yaml"
        }
    }
}
