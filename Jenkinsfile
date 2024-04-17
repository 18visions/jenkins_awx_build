pipeline {
    agent any

    environment {
        // Environment variables for Docker Hub
        DOCKERHUB_USERNAME = '18visions'
        IMAGE_TAG = 'latest' // You can dynamically define this based on build details
    }

    stages {
        stage('Preparation') {
            steps {
                echo 'Cloning repository...'
                git 'https://github.com/ansible/awx.git'
            }
        }

        stage('Build Images') {
            steps {
                script {
                    dir('awx') {
                        // Building Docker image
                        sh 'make docker-compose-build' // This command builds the necessary Docker image as per the AWX Makefile
                    }
                }
            }
        }

        stage('Docker Login Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        echo 'Logged in to Docker Hub'
                    }
                }
            }
        }

        stage('Tag and Push to Docker Hub') {
            steps {
                script {
                    // Tagging the image for Docker Hub
                    sh "docker tag awx ${DOCKERHUB_USERNAME}/awx:${IMAGE_TAG}"

                    // Pushing the image to Docker Hub
                    sh "docker push ${DOCKERHUB_USERNAME}/awx:${IMAGE_TAG}"
                }
            }
        }


        stage('Post-build') {
            steps {
                echo 'Build and deployment complete.'
            }
        }
    }

    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'Build succeeded!'
        }
        failure {
            echo 'Build failed!'
        }
        unstable {
            echo 'Something is wrong with the build'
        }
        changed {
            echo 'Changes detected in the build'
        }
    }
}
