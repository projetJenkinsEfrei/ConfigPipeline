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
                        branch: "PRB-DEV"

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
                    sh"packer build Packer/buildAMI.json"
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
                        branch: "PRB-DEV"
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
                    sh"terraform init"
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
                        branch: "PRB-DEV"
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
                    sh"terraform init"
                    sh"terraform apply -auto-approve -var 'env=${env.JOB_BASE_NAME}'"
                }
            }
        } 
    }
}