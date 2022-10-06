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
  stage('========== Deploy ==========') {
    checkout([$class: 'GitSCM',
      branches: [[name: '*/main' ]],
      extensions: scm.extensions,
      userRemoteConfigs: [[
        url: 'https://github.com/dbswlgp/deploy-repo.git',
        credentialsId: 'github_access_token',
      ]]
    ])
    script {
      sh "sed -i 's/jenkins-nginx:.*/jenkins-nginx:${env.BUILD_NUMBER}/g' nginx-deploy.yaml"
      withCredentials([usernamePassword(credentialsId: 'github_access_token', usernameVariable: 'dbswlgp', passwordVariable:'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')]) {
        sh "git config --global user.name dbswlgp"
        sh "git config --global user.email 26017097@naver.com"
        sh "git add ."
        sh "git commit -m 'image version ${env.BUILD_NUMBER}'"
        sh "git push http://dbswlgp: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@github.com/dbswlgp/deploy-repo HEAD:main"
      }
    }
  }
}
