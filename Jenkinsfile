node {
  stage('========== Clone repository ==========') {
    checkout scm
  }
  stage('========== Build image ==========') {
    app = docker.build("dbswlgp99/jenkins-nginx")
  }
  stage('========== Push image ==========') {
    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_dbswlgp99') {
      app.push("${env.BUILD_NUMBER}")
      app.push("latest")
    }
  }
  stage('========== Manifest update ==========') {
    git branch: "main",
    credentialsId: 'github_access_token',
    url: 'https://github.com/dbswlgp/deploy-repo.git'
    sh "sed -i 's/jenkins-nginx:.*/jenkins-nginx:${env.BUILD_NUMBER}/g' nginx-deploy.yaml"
    sh "git add nginx-deploy.yaml"
    sh "git commit -m '${env.BUILD_NUMBER} image version'"
    withCredentials([gitUsernamePassword(credentialsId: 'github_access_token', gitToolName: 'git-tool')]) {
      sh "git remote set-url origin https://github.com/dbswlgp/deploy-repo"
      sh "git push -u origin main"
    }
  }
}
