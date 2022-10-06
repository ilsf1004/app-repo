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
        credentialsId: 'jenkinds-ssh-private',
      ]]
    ])
    sshagent(credentials: ['jenkinds-ssh-private']){
      sh("""
        #!/usr/bin/env bash
        set +x
        export GIT_SSH_COMMAND="ssh -oStrictHostKeyChecking=no"
        git config --global user.email "26017097@naver.com"
        git checkout main
        sed -i 's/jenkins-nginx:.*/jenkins-nginx:${env.BUILD_NUMBER}/g' nginx-deploy.yaml
        git commit -a -m "updated the image tag"
        git push
      """)
    }
  }
}
