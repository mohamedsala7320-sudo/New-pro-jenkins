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
                    
                    // استخدام المسار المباشر لملف التيرموفورم لكي يعمل مع Jenkins بدون مشاكل
                    bat 'C:\\Users\\user\\AppData\\Local\\Microsoft\\WinGet\\Packages\\HashiCorp.Terraform_Microsoft.Winget.Source_8wekyb3d8bbwe\\terraform.exe init'
                    bat 'C:\\Users\\user\\AppData\\Local\\Microsoft\\WinGet\\Packages\\HashiCorp.Winget.Source_8wekyb3d8bbwe\\terraform.exe workspace select ${params.ENVIRONMENT} || C:\\Users\\user\\AppData\\Local\\Microsoft\\WinGet\\Packages\\HashiCorp.Terraform_Microsoft.Winget.Source_8wekyb3d8bbwe\\terraform.exe workspace new ${params.ENVIRONMENT}'
                    bat 'C:\\Users\\user\\AppData\\Local\\Microsoft\\WinGet\\Packages\\HashiCorp.Terraform_Microsoft.Winget.Source_8wekyb3d8bbwe\\terraform.exe apply -var-file=${params.ENVIRONMENT}.tfvars -auto-approve'
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
                    Build Number: ${env.BUILD_NUMBER}
                    Build URL: ${env.BUILD_URL}
                """,
                to: 'mohamedsala7320@gmail.com'
            )
        }
    }
}