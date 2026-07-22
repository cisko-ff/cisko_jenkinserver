pipeline {
  agent any

  environment {
    AZ_ACCOUNT   = 'cisko'
    AZ_SHARE     = 'milestone'
    STAGING_URL  = 'http://ciskomilestone.eshmare9dugme0a4.eastus.azurecontainer.io/'
    WEB_SERVER_1 = '10.0.3.167'
    WEB_SERVER_2 = '10.0.4.100'
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Deploy to Staging (Azure ACI)') {
      steps {
        withCredentials([string(credentialsId: 'milestoneic09', variable: 'AZ_KEY')]) {
          sh '''
            az storage file upload-batch \
              --account-name "$AZ_ACCOUNT" \
              --account-key "$AZ_KEY" \
              --destination "$AZ_SHARE" \
              --source . --no-progress
          '''
        }
      }
    }

    stage('Test Staging') {
      steps {
        sh '''
          sleep 10
          HTTP_CODE=$(curl -s -o /dev/null -w "%{http_code}" "$STAGING_URL")
          if [ "$HTTP_CODE" != "200" ]; then
            echo "FAIL: Staging returned HTTP $HTTP_CODE"
            exit 1
          fi
          echo "PASS: Staging returned HTTP 200"
        '''
      }
    }

    stage('Deploy to Production (AWS EC2s)') {
      steps {
        sshagent(credentials: ['web-server-ssh-key']) {
          sh '''
            scp -o StrictHostKeyChecking=no *.html ubuntu@$WEB_SERVER_1:/home/ubuntu/
            ssh -o StrictHostKeyChecking=no ubuntu@$WEB_SERVER_1 \
              'sudo cp /home/ubuntu/*.html /var/www/html/'

            scp -o StrictHostKeyChecking=no *.html ubuntu@$WEB_SERVER_2:/home/ubuntu/
            ssh -o StrictHostKeyChecking=no ubuntu@$WEB_SERVER_2 \
              'sudo cp /home/ubuntu/*.html /var/www/html/'
          '''
        }
      }
    }
  }
}
