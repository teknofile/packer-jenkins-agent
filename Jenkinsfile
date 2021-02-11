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

    stage("Validate Template") {
      steps {
        echo "Testing to make sure that the json is right"
        sh "/usr/local/bin/packer validate jenkins-agent-ubuntu-x86_64.json"
      }
    }

    stage("Build the AMI") {
      steps {
        echo "Building the AMI"
        // TODO: "With Credentials"
        withCredentials([usernamePassword(credentialsId: 'aws-tkf-sharedservices', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
        // sh "AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID} AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY} ./packer build jenkins-agent-ubuntu-x86_64.json"
          sh "/usr/local/bin/packer build jenkins-agent-ubuntu-x86_64.json"
        }
      }
    }
  }
}
