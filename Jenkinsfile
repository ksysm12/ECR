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
