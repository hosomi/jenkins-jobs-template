node('linux-slave') {
  stage('clean workspace') {
    deleteDir()
  }
  stage('checkout') {
    checkout([$class: 'GitSCM', branches: [[name: "*/master"]], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory']], submoduleCfg: [], userRemoteConfigs: [[url: "https://github.com/hosomi/AzureStorageTableCoreLogger.git"]]])
  }
  stage('build') {
    sh label: '', script: '''dotnet build --configuration Release '''
  }
  stage('build : artifacts') {
    dir('AzureStorageTableCoreLogger/bin/Release/netcoreapp3.1') {
      archiveArtifacts '*'
      fingerprint '*'
    }
  }
}