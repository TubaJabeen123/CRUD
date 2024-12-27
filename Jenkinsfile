pipeline {
    agent any

    environment {
        REMOTE_HOST = '13.51.235.84'
        DEPLOY_USER = 'ubuntu'
        REMOTE_PATH = '/var/www/html/'
    }

    stages {
        stage('Deploy Website Files') {
            steps {
                sshagent(['apache-deploy-key']) {
                    script {
                        echo 'Deploying website files to the remote Apache server...'
                        
                        // Add the server's SSH key to known_hosts
                        sh "ssh-keyscan -H ${REMOTE_HOST} >> ~/.ssh/known_hosts"

                        // Copy files to a temporary directory and move them using sudo
                        sh """
                        scp index.html ${DEPLOY_USER}@${REMOTE_HOST}:/tmp/
                        scp styles.css ${DEPLOY_USER}@${REMOTE_HOST}:/tmp/
                        scp script.js ${DEPLOY_USER}@${REMOTE_HOST}:/tmp/
                        ssh ${DEPLOY_USER}@${REMOTE_HOST} "sudo mv /tmp/index.html ${REMOTE_PATH} && sudo mv /tmp/styles.css ${REMOTE_PATH} && sudo mv /tmp/script.js ${REMOTE_PATH}"
                        """
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    echo 'Verifying website deployment on the remote server...'

                    // Verify the files are served correctly
                    sh """
                    curl -I http://${REMOTE_HOST}/index.html
                    curl -I http://${REMOTE_HOST}/styles.css
                    curl -I http://${REMOTE_HOST}/script.js
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
            echo 'Deployment failed. Check the logs for more details.'
        }
    }
}




// pipeline {
//     agent any

//     environment {
//         REMOTE_HOST = '13.51.235.84' // Replace with your Apache server IP
//         DEPLOY_USER = 'ubuntu'      // Replace with your deployment user
//         REMOTE_PATH = '/var/www/html/' // Path to deploy files on the server
//     }

//     stages {
//         stage('Checkout Code') {
//             steps {
//                 checkout scm
//             }
//         }

//         stage('Prepare Deployment') {
//             steps {
//                 script {
//                     // Ensure the known_hosts file is updated with the correct host key
//                     sh """
//                     ssh-keygen -R ${REMOTE_HOST} || true
//                     ssh-keyscan -H ${REMOTE_HOST} >> /var/lib/jenkins/.ssh/known_hosts
//                     """
//                 }
//             }
//         }

// stage('Deploy Website') {
//     steps {
//         sshagent(['apache-deploy-key']) { // Replace with your SSH credential ID
//             sh """
//             echo "Deploying website files to ${REMOTE_HOST}..."
//             scp index.html ${DEPLOY_USER}@${REMOTE_HOST}:${REMOTE_PATH}/temp_index.html
//             scp styles.css ${DEPLOY_USER}@${REMOTE_HOST}:${REMOTE_PATH}/temp_styles.css
//             scp script.js ${DEPLOY_USER}@${REMOTE_HOST}:${REMOTE_PATH}/temp_script.js
//             ssh ${DEPLOY_USER}@${REMOTE_HOST} "sudo mv ${REMOTE_PATH}/temp_index.html ${REMOTE_PATH}/index.html && sudo mv ${REMOTE_PATH}/temp_styles.css ${REMOTE_PATH}/styles.css && sudo mv ${REMOTE_PATH}/temp_script.js ${REMOTE_PATH}/script.js && sudo chown www-data:www-data ${REMOTE_PATH}* && sudo chmod 644 ${REMOTE_PATH}*"
//             """
//         }
//     }
// }


//         stage('Verify Deployment') {
//             steps {
//                 sshagent(['apache-deploy-key']) {
//                     sh """
//                     echo "Verifying deployment..."
//                     ssh ${DEPLOY_USER}@${REMOTE_HOST} "ls -l ${REMOTE_PATH}"
//                     """
//                 }
//             }
//         }
//     }

//     post {
//         success {
//             echo 'Deployment completed successfully!'
//         }
//         failure {
//             echo 'Deployment failed. Please check the logs for details.'
//         }
//     }
// }
