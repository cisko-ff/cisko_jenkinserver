pipeline {

agent any

environment {

AZ_ACCOUNT = 'milestoneic'

AZ_SHARE = 'milestone'

}

stages {

stage('Checkout') { steps { checkout scm } }

stage('Deploy to ACI (file share)') {

steps {

withCredentials([string(credentialsId: 'milestoneic09', variable: 'AZ_KEY')]) {

sh '''

az storage file upload-batch \

--account-name "milestoneic09" --account-key "XIIWMSBei6XpT6IZEWsWPCUnXlqX/5XoAcVVBU/zO4gV2uSqUJXHUQe9EGLdRUsUkQUG3vUqDePr+ASt6P29bA==" \

--destination "milestone" --source . \

--pattern "*.html" --no-progress

'''

}

}

}

}

}‌
