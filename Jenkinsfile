pipeline {
    agent any

    parameters {
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'stg', 'prod'],
            description: 'Chose the selected environment'
        )
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Terraform Apply with Admin Credentials') {
            steps {
                withCredentials([
                    string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    
                    // تشغيل أوامر Terraform مباشرة على ويندوز بدون wsl
                    bat 'terraform init'
                    bat "terraform workspace select ${params.ENVIRONMENT} || terraform workspace new ${params.ENVIRONMENT}"
                    bat "terraform apply -var-file=${params.ENVIRONMENT}.tfvars -auto-approve"
                }
            }
        }
    }

    post {
        failure {
            mail(
                subject: "Jenkins Pipeline Failed: ${env.JOB_NAME}",
                body: """
                    The Jenkins pipeline has failed during Terraform execution.

                    Environment: ${params.ENVIRONMENT}
                    Job: ${env.JOB_NAME}
                    Build Number: ${env.BUILD_NUMBER}
                    Build URL: ${env.BUILD_URL}
                """,
                to: 'mohamedsala7320@gmail.com'
            )
        }
    }
}