stage('echo-parallel') {
  parallel(
    "echo-master": {
      node('master') {
        sh label: '', script: 'echo "Hello, World! from master."'
      }
    }
    ,"echo-linux-slave": {
      node('linux-slave') {
        sh label: '', script: 'echo "Hello, World! from linux-slave."'
      }
    }
  )
}