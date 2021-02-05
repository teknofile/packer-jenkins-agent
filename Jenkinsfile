pipeline {
  agent {
    label 'X86_64'
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '10', daysToKeepStr: '60'))
    parallelsAlwaysFailFast()
  }

  enviornment {
    TKF_USER = 'teknofile'
  }

  stages {
    stage("Get Packer") {
      steps {
        echo "Obtaining the latest packer binary on ${NODE_NAME}"
        sh "curl -LO https://raw.github.com/robertpeteuil/packer-installer/master/packer-install.sh"
        sh "chmod +x packer-install.sh"
        sh "./packer-install.sh -c"
    }
  }
}
