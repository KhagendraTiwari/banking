pipeline {
    agent { label 'ec2-agent' } // Label for Jenkins agent

    environment {
        AWS_REGION = 'us-east-1'
        TF_VAR_aws_region = "${AWS_REGION}"
        TF_VAR_key_pair = 'your-ec2-keypair'
        ANSIBLE_HOST_KEY_CHECKING = 'False'
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Checkout repository with Terraform and Ansible files
                git branch: 'main', url: 'https://github.com/KhagendraTiwari/banking.git'
            }
        }

        stage('Terraform Init and Plan') {
            steps {
                dir('terraform') {
                    sh 'terraform init'
                    sh 'terraform plan -out=tfplan'
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                dir('terraform') {
                    sh 'terraform apply -auto-approve'
                }
            }
        }

        stage('Configure EC2 Instances with Ansible') {
            steps {
                dir('ansible') {
                    // Use dynamic inventory based on Terraform output
                    sh 'ansible-playbook -i inventory/hosts playbook.yml'
                }
            }
        }

        stage('Optional: Deploy Docker Containers') {
            steps {
                dir('docker') {
                    // Deploy Docker containers if required
                    sh 'ansible-playbook -i inventory/hosts deploy_containers.yml'
                }
            }
        }
    }

    post {
        always {
            // Cleanup Terraform resources if necessary
            dir('terraform') {
                sh 'terraform destroy -auto-approve'
            }
        }
    }
}
