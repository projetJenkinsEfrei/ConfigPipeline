pipeline {
    agent any
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
                        credentialsId:'4fcb3ce4-727f-415e-8bc3-8e5202658e10',
                        branch: "MPI-DEV"
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

    }
}