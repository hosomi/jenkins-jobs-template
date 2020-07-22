node('linux-slave') {
  stage('clean workspace') {
    deleteDir()
  }
  stage('checkout') {
    checkout([$class: 'GitSCM', branches: [[name: "*/master"]], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory']], submoduleCfg: [], userRemoteConfigs: [[url: "https://github.com/hosomi/angular10-from-angular9-ngrx-example.git"]]])
  }
  stage('npm install') {
    nodejs('NodeJS 12.18.2') {
      sh label: '', script: '''npm i'''
    }
  }
  stage('build') {
    nodejs('NodeJS 12.18.2') {
      sh label: '', script: '''node ${NODEJS_HOME}/bin/ng build '''
    }
  }
  stage('build : artifacts') {
    dir('dist') {
      archiveArtifacts '*'
      fingerprint '*'
    }
  }
}