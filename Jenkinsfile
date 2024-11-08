pipeline {
    environment {
        registry = "damiicodes/sprint-app"
        registryCredentials = "dockerhub_id"
        frontendImage = ""
        backendImage = ""
        BACKEND = 'https://github.com/campanula/lbg-car-spring-app-starter.git'
        FRONTEND = 'https://github.com/campanula/lbg-car-react-starter.git'
    }
    agent any
    tools {        
        maven 'M3'     
    }
    stages {
        
        stage('Setup backend') {
            steps {
                script {
                    dir('backend'){
                        git url: "${BACKEND}", branch: 'main'
                        backendImage = docker.build("${registry}-backend:${env.BUILD_NUMBER}", '-f Dockerfile .')
                    }
            
                }
            }
        }

        stage('Setup frontend') {
            steps {
                script {
                    dir('frontend') {
                        git url: "${FRONTEND}", branch: 'main'
                        frontendImage = docker.build("${registry}-frontend:${env.BUILD_NUMBER}", '-f Dockerfile .')
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    dir('backend'){
                        docker.withRegistry('', registryCredentials) {
                            // Push the image with both the build number and latest tags
                            backendImage.push("${env.BUILD_NUMBER}")
                            backendImage.push("latest")
                            }
                        }
                    dir('frontend'){
                        docker.withRegistry('', registryCredentials) {
                            // Push the image with both the build number and latest tags
                            frontendImage.push("${env.BUILD_NUMBER}")
                            frontendImage.push("latest")
                            }
                        }

                    }
                }
            }

        stage('Clean up') {
            steps {
                script {
                    sh 'docker image prune --all --force --filter "until=48h"'
                }
            }
        }
    }
}

