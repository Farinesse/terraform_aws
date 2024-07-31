pipeline {
    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    }
    environment {
        AWS_ACCESS_KEY_ID     = credentials('Aws_Access_key_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_Secret_access_key')
    }
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Farinesse/terraform_aws.git'
            }
        }
        stage('Init') {
            steps {
                dir('terraform') {
                    sh 'terraform init'
                }
            }
        }
        stage('Plan') {
            steps {
                dir('terraform') {
                    sh 'terraform plan -out tfplan'
                    sh 'terraform show -no-color tfplan > tfplan.txt'
                }
            }
        }
        stage('Approval') {
            when {
                not {
                    equals expected: true, actual: params.autoApprove
                }
            }
            steps {
                script {
                    def plan = readFile 'terraform/tfplan.txt'
                    input message: "Do you want to apply the plan?", parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
                }
            }
        }
        stage('Apply') {
            steps {
                dir('terraform') {
                    sh 'terraform apply -input=false tfplan'
                }
            }
        }
    }
}
