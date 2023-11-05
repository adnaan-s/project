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
                    sh '''
                    docker run -d -p 80:80 --name application adnaansidd/prod:lts
                    '''
                    }
                }
            }
        }
    }
