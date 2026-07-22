pipeline {
    agent any

    environment {
        AZ_ACCOUNT = 'milestoneic09'
        AZ_SHARE = 'milestone'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy to Azure File Share') {
            steps {
                withCredentials([
                    string(
                        credentialsId: 'milestoneic09',
                        variable: 'AZURE_STORAGE_KEY'
                    )
                ]) {
                    sh '''
                        az storage file upload-batch \
                          --account-name "$AZ_ACCOUNT" \
                          --account-key "$AZURE_STORAGE_KEY" \
                          --destination "$AZ_SHARE" \
                          --source . \
                          --pattern "*.html" \
                          --no-progress
                    '''
                }
            }
        }
    }
}
