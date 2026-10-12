pipeline {
    agent any

    // تعريف الـ Choice Parameter لاختيار البيئة (dev, stg, prod)
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
                // استدعاء المفاتيح المخزنة كـ Secret text في Jenkins
                withCredentials([
                    string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    
                    // استخدام المسار الكامل لـ wsl.exe لتجنب مشاكل تعرف النظام عليه
                    bat 'C:\\Windows\\System32\\wsl.exe terraform init'
                    
                    // اختيار الـ Workspace أو إنشاؤها إن لم تكن موجودة
                    bat "C:\\Windows\\System32\\wsl.exe terraform workspace select ${params.ENVIRONMENT} || C:\\Windows\\System32\\wsl.exe terraform workspace new ${params.ENVIRONMENT}"
                    
                    // تطبيق التغييرات باستخدام ملف الـ tfvars الخاص بالبيئة المختارة
                    bat "C:\\Windows\\System32\\wsl.exe terraform apply -var-file=${params.ENVIRONMENT}.tfvars -auto-approve"
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