#이미지 빌드시 이름을 ECR 쪽으로 변경
app = docker.build("035884387009.dkr.ecr.ap-northeast-1.amazonaws.com/test")

# ECR에서 생성한 Repository URI로 변경 및 Jenkins AWS Credential으로 변경
docker.withRegistry('https://035884387009.dkr.ecr.ap-northeast-1.amazonaws.com', 'ecr:ap-northeast-1:canape1128')

# Full Code
  
node {
     stage('Clone repository') {
         checkout scm
     }

     stage('Build image') {
         app = docker.build("035884387009.dkr.ecr.ap-northeast-1.amazonaws.com/test")
     }

     stage('Push image') {
         sh 'rm  ~/.dockercfg || true'
         sh 'rm ~/.docker/config.json || true'
         
         docker.withRegistry('https://035884387009.dkr.ecr.ap-northeast-1.amazonaws.com', 'ecr:ap-northeast-1:canape1128') {
             app.push("${env.BUILD_NUMBER}")
             app.push("latest")
     }
  }
}
