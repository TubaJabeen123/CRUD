pipeline {
    agent any

    environment {
        REMOTE_HOST = '13.49.72.39' // Replace with your Apache server IP
        DEPLOY_USER = 'ubuntu'      // Replace with your deployment user
        REMOTE_PATH = '/var/www/html/' // Path to deploy files on the server
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Prepare Deployment') {
            steps {
                script {
                    // Ensure the known_hosts file is updated with the correct host key
                    sh """
                    ssh-keygen -R ${REMOTE_HOST} || true
                    ssh-keyscan -H ${REMOTE_HOST} >> /var/lib/jenkins/.ssh/known_hosts
                    """
                }
            }
        }

        stage('Deploy Website') {
            steps {
                sshagent(['apache-deploy-key']) { // Replace with your SSH credential ID
                    sh """
                    echo "Deploying website files to ${REMOTE_HOST}..."
                    scp index.html ${DEPLOY_USER}@${REMOTE_HOST}:${REMOTE_PATH}
                    scp styles.css ${DEPLOY_USER}@${REMOTE_HOST}:${REMOTE_PATH}
                    scp script.js ${DEPLOY_USER}@${REMOTE_HOST}:${REMOTE_PATH}
                    ssh ${DEPLOY_USER}@${REMOTE_HOST} "sudo chown www-data:www-data ${REMOTE_PATH}* && sudo chmod 644 ${REMOTE_PATH}*"
                    """
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                sshagent(['apache-deploy-key']) {
                    sh """
                    echo "Verifying deployment..."
                    ssh ${DEPLOY_USER}@${REMOTE_HOST} "ls -l ${REMOTE_PATH}"
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed. Please check the logs for details.'
        }
    }
}
