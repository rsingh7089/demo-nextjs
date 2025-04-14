pipeline {
    agent any

    environment {
        SSH_KEY = 'ec2-server-key' // Your Jenkins SSH credential ID
        EC2_HOST = 'ubuntu@13.233.104.95' // Replace with your EC2 username + IP
        DEPLOY_DIR = '/var/www/html' // Nginx directory to serve from
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/rsingh7089/demo-nextjs.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
                sh 'npm run export' // For static site. Output goes to 'out/' folder
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(credentials: [SSH_KEY]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${EC2_HOST} 'sudo rm -rf ${DEPLOY_DIR}/*'
                        scp -o StrictHostKeyChecking=no -r out/* ${EC2_HOST}:${DEPLOY_DIR}/
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment complete!'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}
