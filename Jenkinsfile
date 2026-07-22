pipeline {
 
    agent any
 
    environment {
        AZ_ACCOUNT = 'milestoneic09'
        AZ_SHARE = 'milestone'
        STAGING_URL = 'http://ciskomilestone.eshmare9dugme0a4.eastus.azurecontainer.io/'
    }
 
    stages {
 
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
 
        stage('Deploy to Staging') {
            steps {
 
                withCredentials([
                    string(credentialsId: 'azure-storage-key', variable: 'AZ_KEY')
                ]) {
 
                    sh '''
                    az storage file upload-batch \
                      --account-name "milestoneic09" \
                      --account-key "XIIWMSBei6XpT6IZEWsWPCUnXlqX/5XoAcVVBU/zO4gV2uSqUJXHUQe9EGLdRUsUkQUG3vUqDePr+ASt6P29bA==" \
                      --destination "milestone" \
                      --source . \
                      --pattern "*.html" \
                      --no-progress
                    '''
                }
            }
        }
 
        stage('Test Staging Availability') {
            steps {
                sh '''
                curl -f $STAGING_URL
                '''
            }
        }
 
        stage('Validate Staging Content') {
            steps {
 
                script {
 
                    def content = sh(
                        script: "curl -s http://ciskomilestone.eshmare9dugme0a4.eastus.azurecontainer.io/",
                        returnStdout: true
                    ).trim()
 
                    if (!content.contains("Hello")) {
                        error("Staging content validation failed")
                    }
 
                    echo "Staging validation passed"
                }
            }
        }
 
        stage('Deploy Production') {
 
            steps {
 
                sshagent(credentials: ['milestoneic09']) {
 
                    sh '''
                  scp -o StrictHostKeyChecking=no index.html ubuntu@10.0.142.29:/var/www/html/
 
                  scp -o StrictHostKeyChecking=no index.html ubuntu@10.0.151.178:/var/www/html/
                    '''
                }
 
            }
        }
    }
 
    post {
 
        success {
            echo 'Deployment completed successfully'
        }
 
        failure {
            echo 'Pipeline failed. Production deployment blocked.'
        }
    }
}
