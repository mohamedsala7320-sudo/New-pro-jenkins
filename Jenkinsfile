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
                    
                    // 1. تهيئة Terraform
                    bat 'wsl terraform init'
                    
                    // 2. اختيار الـ Workspace أو إنشاؤها إن لم تكن موجودة
                    bat "wsl terraform workspace select ${params.ENVIRONMENT} || wsl terraform workspace new ${params.ENVIRONMENT}"
                    
                    // 3. تطبيق التغييرات باستخدام ملف الـ tfvars الخاص بالبيئة المختارة
                    bat "wsl terraform apply -var-file=${params.ENVIRONMENT}.tfvars -auto-approve"
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