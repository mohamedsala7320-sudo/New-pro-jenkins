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
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Terraform Apply on LocalStack') {
            steps {
                // استخدام بيانات وهمية لـ LocalStack لتجاوز الـ withCredentials
                withCredentials([
                    string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    
                    // استخدام المسار المباشر والصحيح لملف terraform.exe
                    bat '"C:\\Users\\user\\AppData\\Local\\Microsoft\\WinGet\\Packages\\HashiCorp.Terraform_Microsoft.Winget.Source_8wekyb3d8bbwe\\terraform.exe" init'
                    
                    bat '"C:\\Users\\user\\AppData\\Local\\Microsoft\\WinGet\\Packages\\HashiCorp.Terraform_Microsoft.Winget.Source_8wekyb3d8bbwe\\terraform.exe" workspace select %ENVIRONMENT% || "C:\\Users\\user\\AppData\\Local\\Microsoft\\WinGet\\Packages\\HashiCorp.Terraform_Microsoft.Winget.Source_8wekyb3d8bbwe\\terraform.exe" workspace new %ENVIRONMENT%'
                    
                    bat '"C:\\Users\\user\\AppData\\Local\\Microsoft\\WinGet\\Packages\\HashiCorp.Terraform_Microsoft.Winget.Source_8wekyb3d8bbwe\\terraform.exe" apply -var-file=%ENVIRONMENT%.tfvars -auto-approve'
                }
            }
        }
    }
}