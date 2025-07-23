pipeline {
  agent {
    docker {
      image 'node:22'
    }

  }
  stages {
    stage('Info') {
      steps {
        sh 'node -v'
      }
    }

    stage('Download cortexcli') {
      steps {
        sh '''
          crtx_resp=$(curl "${CORTEX_API_URL}/public_api/v1/unified-cli/releases/download-link?os=linux&architecture=amd64"             -H "x-xdr-auth-id: $CORTEX_API_KEY_ID"             -H "Authorization: $CORTEX_API_KEY")
          crtx_url=$(echo $crtx_resp | jq -r ".signed_url")
          curl -o cortexcli $crtx_url
          chmod +x cortexcli
          ./cortexcli --version
        '''
      }
    }

    stage('Run Scan') {
      steps {
        sh '''
          ./cortexcli             --api-base-url "$CORTEX_API_URL"             --api-key "$CORTEX_API_KEY"             --api-key-id "$CORTEX_API_KEY_ID"             code scan             --directory "$(pwd)"             --repo-id "my-org/my-repo"             --branch "main"             --source "JENKINS"             --upload-mode "no-upload"             --framework "sca"             --create-repo-if-missing
        '''
      }
    }

  }
}