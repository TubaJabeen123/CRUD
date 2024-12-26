pipeline {
    agent any
    stages {
        stage('Deploy Website') {
            steps {
                sshagent(['apache-deploy-key']) {
                    script {
                        echo 'Deploying website files to remote Apache server...'
                        
                        sh 'ssh-keyscan -H 13.51.235.84 >> ~/.ssh/known_hosts'
                        
                        // Copy HTML, CSS, and JS files
                        sh '''
                        scp index.html ubuntu@13.51.235.84:/var/www/html/
                        scp styles.css ubuntu@13.51.235.84:/var/www/html/
                        scp script.js ubuntu@13.51.235.84:/var/www/html/
                        '''
                        
                        // Ensure proper permissions
                        sh '''
                        ssh ubuntu@13.49.72.39 "sudo chown www-data:www-data /var/www/html/* && sudo chmod 644 /var/www/html/*"
                        '''
                    }
                }
            }
        }
        stage('Verify Deployment') {
            steps {
                script {
                    echo 'Verifying deployment on remote server...'
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
