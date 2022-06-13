pipeline {
  agent {
    label 'go16'
  }
  stages {
    stage('go build') {
      agent none
      steps {
        container('go') {
          withCredentials([usernamePassword(credentialsId : 'git-joywok-huwei' ,usernameVariable : 'GIT_USERNAME' ,passwordVariable : 'GIT_PASSWORD' ,)]) {
            sh 'git config --global credential.username ${GIT_USERNAME} && git config --global credential.helper "!echo password=${GIT_PASSWORD}; echo"'
          }

          sh 'rm -f go.sum && go mod tidy'
        }

      }
    }

    stage('default-1') {
      parallel {
        stage('deploy staging') {
          agent none
          when {
            branch 'dev'
          }
          steps {
            container('go') {
              sh 'make build docker push apply_staging'
            }

          }
        }

        stage('deploy uat') {
          agent none
          when {
            branch 'master'
          }
          steps {
            container('go') {
              sh 'make build docker-uat push-uat apply_uat'
            }

          }
        }

      }
    }

  }
  environment {
    GO111MODULE = 'on'
    GOPRIVATE = 'git.joywok.com'
    GOPROXY = 'http://goproxy.cn,https://goproxy.io'
  }
}
