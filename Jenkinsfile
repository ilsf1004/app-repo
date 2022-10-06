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
      sh "git config --global user.name dbswlgp"
      sh "git config --global user.email 26017097@naver.com"
      withCredentials([usernamePassword(credentialsId: 'github_access_token', usernameVariable: 'dbswlgp', passwordVariable:''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)]) {
        sh "git add ."
        sh "git commit -m 'image version ${env.BUILD_NUMBER}'"
        sh "git push https://dbswlgp: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@github.com/dbswlgp/deploy-repo HEAD:main"
      }
    }
  }
}
