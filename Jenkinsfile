pipeline {
  agent {
    label 'linux'
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '10', daysToKeepStr: '60'))
    parallelsAlwaysFailFast()
  }
  stages {
    stage("Setup Enviornment") {
      steps {
        script {
          env.EXIT_STATUS = ''
          env.COMMIT_SHA = sh(
            script: '''git rev-parse HEAD''',
            returnStdout: true).trim()
        }
      }
    }

    stage("Get Packer") {
      steps {
        echo "Obtaining the latest packer binary on ${NODE_NAME}"
        sh "curl -LO https://raw.github.com/robertpeteuil/packer-installer/master/packer-install.sh"
        sh "chmod +x packer-install.sh"
        sh "./packer-install.sh -c"
        sh "ls -alh"
      }
    }

    stage("Validate Template") {
      steps {
        echo "Testing to make sure that the json is right"
        sh "./packer validate jenkins-agent-ubuntu-x86_64.json"
      }
    }

    stage("Build the AMI") {
      steps {
        echo "Building the AMI"
        // TODO: "With Credentials"
      }
    }
  }
}
