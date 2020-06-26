pipeline {
    agent any
    options {
        ansiColor('xterm')
    }
    stages {
        stage('Clean workspace')
        {
            steps
            {
                cleanWs()
            }
        }
        stage('Git clone ansible and packer') 
        {
            steps 
            {
                dir('Build_AMI')
                {
                    git(
                        url: 'https://github.com/birintha/CICD_TP_Build_AMI',
                        credentialsId:'AccountMaster',
                        branch: "BSR-DEV"

                    )
                }
            }
        }
        stage('Build AMI')
        {
            steps
            {
                dir('Build_AMI')
                {
                    sh"packer build -var 'env=${env.JOB_BASE_NAME}' Packer/buildAMI.json"
                }
            }
        }
        stage('Git clone infra deployement') 
        {
            steps 
            {
                dir('Infra_dep')
                {
                    git(
                        url: 'https://github.com/birintha/CICD_TP_Deploy_Infra',
                        credentialsId:'AccountMaster',
                        branch: "BSR-DEV"
                    )
                }
            }
        }
        stage('Apply infra')
        {
            steps
            {
                dir('Infra_dep')
                {
                    sh"terraform init -backend-config='path=/home/ubuntu/terraformState/${env.JOB_BASE_NAME}/infra/terraform.tfstate'"
                    sh"terraform apply -auto-approve -var 'env=${env.JOB_BASE_NAME}' "
                }
            }
        }
        stage('Git clone web deployement') 
        {
            steps 
            {
                dir('Web_dep')
                {
                    git(
                        url: 'https://github.com/birintha/CICD_TP_Deploy_WebApp',
                        credentialsId:'AccountMaster',
                        branch: "BSR-DEV"
                    )
                }
            }
        }
        stage('Apply web')
        {
            steps
            {
                dir('Web_dep')
                {
                    sh"terraform init -backend-config='path=/home/ubuntu/terraformState/${env.JOB_BASE_NAME}/web/terraform.tfstate'"
                    sh"terraform apply -auto-approve -var 'env=${env.JOB_BASE_NAME}'"
                }
            }
        } 
    }
}