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
                        branch: "${env.GIT_BRANCH}"
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
                        branch: "${env.GIT_BRANCH}"
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
                    sh"terraform init -backend-config='path=/home/ubuntu/terraformState/${env.GIT_BRANCH}/infra/terraform.tfstate'"
                    sh"terraform apply -auto-approve -var 'env=${env.GIT_BRANCH}' "
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
                        branch: "${env.GIT_BRANCH}"
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
                    sh"terraform init -backend-config='path=/home/ubuntu/terraformState/${env.GIT_BRANCH}/web/terraform.tfstate'"
                    sh"terraform apply -auto-approve -var 'env=${env.GIT_BRANCH}'"
                }
            }
        } 
        stage('Test')
        {
            steps
            {
               sh"curl -I  http://master-elb-194928174.eu-west-1.elb.amazonaws.com:443/"
            }
        } 
    }
}
