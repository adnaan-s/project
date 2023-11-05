pipeline {
    agent any
    
    tools {
        nodejs 'nodejs'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Check out the code from the 'dev' branch in GitHub
                    checkout([$class: 'GitSCM',
                        branches: [[name: 'master']], 
                        userRemoteConfigs: [[url: 'https://github.com/adnaan-s/project.git']]
                    ])
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    // Run the build script
                    sh 'docker build -t demo .'

                    // Tag the image
                    sh 'docker tag demo:latest adnaansidd/prod:lts'
                    
                    // Push the image to the development Docker Hub repository
                    sh 'docker login -u adnaansidd -p 26122001As@'
                    sh 'docker push adnaansidd/prod:lts'
                }
            }
        }

        stage('deploy') {
            steps {
                // Define your private key for SSH
                    def key = credentials('0bfa3fd8-3b88-4863-b1d3-3e5d19bb6e05')

                    // Copy files to the remote server
                    sh "scp -r -o StrictHostKeyChecking=no -i $key * ubuntu@18.60.83.32:/home/ubuntu/"
                    
                    // SSH into the remote server, install Docker, and run the container
                    sh '''
                    ssh -o StrictHostKeyChecking=no -i $key ubuntu@18.60.83.32 << EOF
                    cd /home/ubuntu
                    sudo apt update
                    sudo apt install -y docker.io docker-compose nodejs npm
                    sudo docker run -d -p 80:80 --name application adnaansidd/prod:lts
                    EOF
                    '''
            }
        }
    }
}
