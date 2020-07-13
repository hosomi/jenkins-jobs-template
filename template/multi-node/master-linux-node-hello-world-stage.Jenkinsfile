node('master') {
  stage('echo-master') {
    sh label: '', script: 'echo "Hello, World! from master."'
  }
}
node('linux-slave') {
  stage('echo-linux-slave') {
    sh label: '', script: 'echo "Hello, World! from linux-slave."'
  }
}