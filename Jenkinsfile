pipeline {
  agent {
    docker {
      image 'node:22' // Node.js 22 image, similar to CircleCI
    }
  }

  environment {
    CORTEX_API_URL = 'https://api-viso-8vocygkchsephm7mczadnc.xdr-qa2-uat.us.paloaltonetworks.com'
    CORTEX_API_KEY = credentials('cortex-api-key')       // Jenkins credential ID (Secret Text)
    CORTEX_API_KEY_ID = credentials('cortex-api-key-id') // Jenkins credential ID (Secret Text)
    MIN_LOG_LEVEL = 'DEBUG'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Download cortexcli') {
      steps {
        sh '''
          set -x
          crtx_resp=$(curl "${CORTEX_API_URL}/public_api/v1/unified-cli/releases/download-link?os=linux&architecture=amd64" \
            -H "x-xdr-auth-id: $CORTEX_API_KEY_ID" \
            -H "Authorization: $CORTEX_API_KEY")
          crtx_url=$(echo $crtx_resp | jq -r ".signed_url")
          curl -o cortexcli $crtx_url
          chmod +x cortexcli
          ./cortexcli --version
        '''
      }
    }

    stage('Run Cortex Scan') {
      steps {
        sh '''
          ./cortexcli \
            --api-base-url "${CORTEX_API_URL}" \
            --api-key "${CORTEX_API_KEY}" \
            --api-key-id "${CORTEX_API_KEY_ID}" \
            code scan \
            --directory "$(pwd)" \
            --repo-id "$(basename $(pwd))" \
            --branch "$GIT_BRANCH" \
            --source "JENKINS" \
            --upload-mode "no-upload" \
            --framework "sca" \
            --create-repo-if-missing
        '''
      }
    }
  }
}
