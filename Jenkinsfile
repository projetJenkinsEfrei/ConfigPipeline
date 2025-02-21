pipeline {
    agent any
    options {
        ansiColor('xterm')
        disableConcurrentBuilds()
    }
    stages {
        stage('test commit')
        {
            steps
            {
                echo 'test commit prez'
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
                        branch: "master"
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
                    sh "terraform init -backend-config='bucket=devops-bucket-pipeline' -backend-config='key=${env.GIT_BRANCH}/Infra_dep/infra.tfstate' -backend-config='region=eu-west-1'"
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
                        branch: "master"
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
                    sh "terraform init -backend-config='bucket=devops-bucket-pipeline' -backend-config='key=${env.GIT_BRANCH}/Web_dep/infra.tfstate' -backend-config='region=eu-west-1'"
                    sh"terraform apply -auto-approve -var 'env=${env.GIT_BRANCH}'"
                }
            }
        } 
        stage('Clean workspace')
        {
            steps
            {
                cleanWs()
            }
        }
    }
}
