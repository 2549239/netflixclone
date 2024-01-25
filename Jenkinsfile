pipeline {
    agent any

    environment {
        PROD_USERNAME = 'ubuntu'
        PROD_SERVER = 'ec2-34-198-52-125.compute-1.amazonaws.com'
        PROD_DIR = '/home/ubuntu/myflix'
        DOCKER_IMAGE_NAME = 'myflix-deployment'
        DOCKER_CONTAINER_NAME = 'myflix'
        DOCKER_CONTAINER_PORT = '8000'
        DOCKER_HOST_PORT = '8000'
    }

    stages {
        stage('Load Code to Workspace') {
            steps {
                // This step automatically checks out the code into the workspace.
                checkout scm             

                // Build logic
                // sh 'mvn clean install' 
            }
        }

        stage('Deploy Repo to Prod. Server') {
            steps {
                script {
                    sh 'echo Packaging files ....'
                    
                    // sh 'ls -l'
                    // sh 'pwd myflix_files.tar.gz'
                    // Archive the repository files
                    sh 'tar -czf myflix_files.tar.gz * || true'
                    sh "ssh -i "key.pem" ubuntu@ec2-44-221-222-164.compute-1.amazonaws.com"
                    // Transfer the zipped repository to the production server
                    sh "scp -o StrictHostKeyChecking=no myflix_files.tar.gz -i key.pem ${PROD_USERNAME}@${PROD_SERVER}:${PROD_DIR}"
                    sh 'echo Files transferred to server. Unpacking ...'
                    sh "ssh -o StrictHostKeyChecking=no ${PROD_USERNAME}@${PROD_SERVER} 'pwd && cd myflix && tar -xzf myflix_files.tar.gz && ls -l'"
                    sh 'echo Repo unloaded on Prod. Server. Preparing to dockerize application ...'
                }
            }
        }

        stage('Dockerize Application') {
            steps {
                script {

                    sh "ssh -o StrictHostKeyChecking=no ${PROD_USERNAME}@${PROD_SERVER} 'cd myflix && docker build -t ${DOCKER_IMAGE_NAME} .'"
                    sh "echo Docker image for myflix rebuilt. Preparing to redeploy container to web..."
                }
            }
        }


         stage('Redeploy Container to Web') {
            steps {
                script {
                    sh "ssh -o StrictHostKeyChecking=no ${PROD_USERNAME}@${PROD_SERVER} 'cd myflix && docker stop ${DOCKER_CONTAINER_NAME} || echo \"Failed to stop container\"'"
                    sh "ssh -o StrictHostKeyChecking=no ${PROD_USERNAME}@${PROD_SERVER} 'cd myflix && docker rm ${DOCKER_CONTAINER_NAME} || echo \"Failed to remove container\"'"

                    sh "echo Container stopped and removed. Preparing to redeploy new version"
                    sh "ssh -o StrictHostKeyChecking=no ${PROD_USERNAME}@${PROD_SERVER} 'cd myflix && docker run -d -p ${DOCKER_HOST_PORT}:${DOCKER_CONTAINER_PORT} --name ${DOCKER_CONTAINER_NAME} ${DOCKER_IMAGE_NAME}'"
                    sh "echo myflix Microservice Deployed!"
                }
            }
        }
    }
}
