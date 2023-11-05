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
                script {
                    // Define your private key for SSH
                    withCredentials([sshUserPrivateKey(credentialsId: '0370ad41-bad8-45f8-b41f-662f28c30faa', keyFileVariable: 'key')]) {
                        def key = sh(script: "cat \$key", returnStdout: true).trim()

                        // SSH into the remote server, install Docker, and run the container
                        sh '''
                        ssh -o StrictHostKeyChecking=no -i $key ubuntu@ec2-18-61-60-62.ap-south-2.compute.amazonaws.com << EOF
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
    }
}

