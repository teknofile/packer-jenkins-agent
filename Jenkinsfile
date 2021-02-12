pipeline {
  agent {
    label 'linux'
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '10', daysToKeepStr: '60'))
    parallelsAlwaysFailFast()
  }
  environment {
    ROLE_ID="54c29b82-d415-dd33-288c-cb07ea43e16d"
    VAULT_ADDR="https://vault.copperdale.teknofile.net"
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

    // The Jenkins node is only allowed to create the wrapped secret ID
    // and with a wrap-ttl between 100s and 300s
    stage("Create Wrapped Secret ID") {
      steps {
        script {
          withCredentials([
            [
              $class: 'VaultTokenCredentialBinding',
              credentialsId: 'Jenkins_Node_Vault_AppRole',
              vaultAddr: 'https://vault.copperdale.teknofile.net'
            ]
          ]){
            env.WRAPPED_SID = sh(
              returnStdout: true,
              script: "vault write -field=wrapping_token -wrap-ttl=200s -f auth/pipeline/role/pipeline-approle/secret-id"
            )
          }
        }
      }
    }

    stage("Unwrapping Secret ID") {
      env.UNWRAPPED_SID = sh(
        returnStdout: true,
        script: "vault unwrap -field=scret_id ${WRAPPED_SID}"
      )
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
