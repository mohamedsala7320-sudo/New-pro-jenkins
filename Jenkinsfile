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
                    
                    // تعريف مسار الـ Terraform في متغير نصي لتسهيل استخدامه بدون أخطاء
                    def tfPath = 'C:\\Users\\user\\AppData\\Local\\Microsoft\\WinGet\\Packages\\HashiCorp.Terraform_Microsoft.Winget.Source_8wekyb3d8bbwe\\terraform.exe'
                    
                    // 1. تهيئة تيرموفورم
                    bat "\"${tfPath}\" init"
                    
                    // 2. اختيار أو إنشاء الـ Workspace للبيئة المحددة
                    bat "\"${tfPath}\" workspace select ${params.ENVIRONMENT} || \"${tfPath}\" workspace new ${params.ENVIRONMENT}"
                    
                    // 3. تطبيق التغييرات بناءً على ملف الـ tfvars الخاص بالبيئة
                    bat "\"${tfPath}\" apply -var-file=${params.ENVIRONMENT}.tfvars -auto-approve"
                }
            }
        }
    }
}