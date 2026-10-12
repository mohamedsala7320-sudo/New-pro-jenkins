pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = "us-east-1"
    }

    parameters {
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'stg', 'prod'],
            description: 'Chose the selected environment'
        )
    }

    stages {
        stage('Set Build Name') {
            steps {
                // هذا السطر يقوم بتغيير اسم البناء في جينكينز ليظهر رقم البناء ومعه اسم البيئة
                currentBuild.displayName = "#${env.BUILD_NUMBER} [${params.ENVIRONMENT}]"
            }
        }

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Terraform Init & Plan') {
            steps {
                withCredentials([
                    string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    bat '"C:\\Users\\user\\AppData\\Local\\Microsoft\\WinGet\\Packages\\HashiCorp.Terraform_Microsoft.Winget.Source_8wekyb3d8bbwe\\terraform.exe" init'
                    
                    bat '"C:\\Users\\user\\AppData\\Local\\Microsoft\\WinGet\\Packages\\HashiCorp.Terraform_Microsoft.Winget.Source_8wekyb3d8bbwe\\terraform.exe" workspace select %ENVIRONMENT% || "C:\\Users\\user\\AppData\\Local\\Microsoft\\WinGet\\Packages\\HashiCorp.Terraform_Microsoft.Winget.Source_8wekyb3d8bbwe\\terraform.exe" workspace new %ENVIRONMENT%'
                    
                    bat '"C:\\Users\\user\\AppData\\Local\\Microsoft\\WinGet\\Packages\\HashiCorp.Terraform_Microsoft.Winget.Source_8wekyb3d8bbwe\\terraform.exe" plan -var-file=%ENVIRONMENT%.tfvars -out=tfplan'
                }
            }
        }

        stage('Manual Approval') {
            steps {
                input message: "Do you want to apply the Terraform changes for environment: ${params.ENVIRONMENT}?",
                      ok: "Proceed to Apply"
            }
        }

        stage('Terraform Apply') {
            steps {
                withCredentials([
                    string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    bat '"C:\\Users\\user\\AppData\\Local\\Microsoft\\WinGet\\Packages\\HashiCorp.Terraform_Microsoft.Winget.Source_8wekyb3d8bbwe\\terraform.exe" apply tfplan'
                }
            }
        }
    }
}