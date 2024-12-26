pipeline {
    agent any
    stages {
        stage('Deploy HTML') {
            steps {
                sshagent(['apache-deploy-key']) { // Use the correct SSH credentials ID
                    script {
                        echo 'Deploying index.html to remote Apache server...'
                        
                        // Add the server's SSH key to known hosts
                        sh 'ssh-keyscan -H 13.51.235.84>> ~/.ssh/known_hosts'
                        
                        // Copy the file to the remote server using SCP
                        sh 'scp index.html ubuntu@13.51.235.84:/var/www/html/'
                    }
                }
            }
        }
        stage('Verify Deployment') {
            steps {
                script {
                    echo 'Verifying index.html deployment on remote server...'
                    
                    // Verify the file is served correctly using curl
                    sh 'curl http://13.51.235.84/index.html'
                }
            }
        }
    }
    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed. Check the logs for more details.'
        }
    }
}
