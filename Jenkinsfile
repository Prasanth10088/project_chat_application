pipeline {
    agent any

    environment {
        S3_BUCKET = 'infoez-frontend'
        DISTRIBUTION_ID = 'E3FI9EBVW77W50'
    }

    stages {

        stage('Git Clone') {
            steps {
                git(
                    branch: 'qa',
                    credentialsId: 'github-credentials',
                    url: 'https://github.com/Infoez-kiaq/Frontend.git'
                )
            }
        }

        stage('NPM Install') {
            steps {
                sh 'npm install'
            }
        }

        stage('NPM Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('S3 Deploy') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-cred']
                ]) {
                    sh 'aws s3 sync build/ s3://$S3_BUCKET --delete'
                }
            }
        }

        stage('CloudFront Invalidation') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-cred']
                ]) {
                    sh 'aws cloudfront create-invalidation --distribution-id $DISTRIBUTION_ID --paths "/*"'
                }
            }
        }
    }
}
