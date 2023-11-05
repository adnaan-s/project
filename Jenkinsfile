pipeline {
    agent any

    tools {
        nodejs 'nodejs'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Check out the code from the 'main' branch in GitHub
                    checkout([$class: 'GitSCM', 
                        branches: [[name: 'main']], 
                        userRemoteConfigs: [[url: 'https://github.com/adnaan-s/project.git']]
                    ])
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    // Run the build script
                    sh 'docker build -t demo2 .'

                    // Tag the image
                    sh 'docker tag demo2:latest adnaansidd/prod:lts'
                    
                    // Push the image to the development Docker Hub repository
                    sh 'docker login -u adnaansidd -p 26122001As@'
                    sh 'docker push adnaansidd/dev:lts'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Define your private key for SSH
                    def key = credentials('key')

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
