node {
  stage('========== Clone repository ==========') {
    checkout scm
  }
  stage('========== Build image ==========') {
    app = docker.build("dbswlgp99/php-apache")
  }
  stage('========== Push image ==========') {
    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_dbswlgp99') {
      app.push("${env.BUILD_NUMBER}")
      app.push("latest")
    }
  }
}
