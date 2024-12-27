pipeline {
    agent any

    environment {
        APACHE_SERVER_IP = '13.51.235.84'  // Apache server's IP
        SSH_CREDENTIAL_ID = 'apache-deploy-key'       // Jenkins SSH Credential ID
        DEPLOY_PATH = '/var/www/html/'     // Apache's default document root
    }

    triggers {
        // Trigger the pipeline when a GitHub push event is received
        githubPush()
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    echo "Cloning repository..."
                    // Clone the GitHub repository
                    checkout scm
                }
            }
        }

        stage('Setup SSH Known Hosts') {
            steps {
                sshagent(["${SSH_CREDENTIAL_ID}"]) {
                    script {
                        echo "Adding ${APACHE_SERVER_IP} to known_hosts..."
                        sh "ssh-keyscan -H ${APACHE_SERVER_IP} >> ~/.ssh/known_hosts"
                    }
                }
            }
        }

        stage('Deploy HTML Files to Apache Server') {
            steps {
                sshagent(["${SSH_CREDENTIAL_ID}"]) {
                    script {
                        echo "Deploying files to ${APACHE_SERVER_IP}..."

                        // Copy all files in the repository to the Apache server
                        sh "scp -r * ubuntu@${APACHE_SERVER_IP}:${DEPLOY_PATH}"
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    echo "Verifying deployment of files on remote server..."

                    // Verify each HTML file in the repository
                    sh "ls *.html | xargs -I {} curl -f http://${APACHE_SERVER_IP}/{}"
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully after Git commit!'
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
