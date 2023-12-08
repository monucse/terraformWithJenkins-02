pipeline {
    agent any
    
    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    } 
    
    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }
    
    stages {
    
        stage('Debug') {
            steps {
                echo "AWS_ACCESS_KEY_ID: ${MY_AWS_ACCESS_KEY}"
            }
        }
        stage('checkout') {
            steps {
                script {
                    dir("terraform") {
                        bat "git clone https://github.com/monucse/terraformWithJenkins-02.git"
                    }
                }
            }
        }
        
        stage('Plan') {
            steps {
                bat 'cd terraform & terraform init'
                bat 'cd terraform & terraform plan -out tfplan'
                bat 'cd terraform & terraform show -no-color tfplan > tfplan.txt'
            }
        }
        
        stage('Approval') {
            when {
                not {
                    expression { params.autoApprove == true }
                }
            }
            
            steps {
                script {
                    def plan = readFile 'terraform/tfplan.txt'
                    input message: "Do you want to apply the plan?",
                          parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
                }
            }
        }
        
        stage('Apply') {
            steps {
                bat 'cd terraform & terraform apply -input=false tfplan'
            }
        }
    }
}
