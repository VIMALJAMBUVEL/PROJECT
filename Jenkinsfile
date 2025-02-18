pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = '26ff175c-215f-4690-ad44-ba4b9a6703b5'
        DOCKER_USER = 'vimaljambuvel'  // Docker Hub username
        DOCKER_PASS = 'Docker@123'  // Docker Hub password
        DOCKER_IMAGE_NAME_DEV = 'vimaljambuvel/dev:latest'  // Docker image name for dev branch
        DOCKER_IMAGE_NAME_PROD = 'vimaljambuvel/prod:latest'  // Docker image name for prod branch
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Get the branch that triggered the build
                    def branch = env.GIT_BRANCH ? env.GIT_BRANCH.replace("origin/", "") : sh(script: "git rev-parse --abbrev-ref HEAD", returnStdout: true).trim()

                    // Checkout the appropriate branch based on the trigger
                    echo "Checking out branch: ${branch}"
                    if (branch == "main") {
                        echo "Checked out 'main' branch"
                        git branch: 'main', url: 'https://github.com/VIMALJAMBUVEL/Project.git'
                    } else {
                        echo "Checked out 'dev' branch"
                        git branch: 'dev', url: 'https://github.com/VIMALJAMBUVEL/Project.git'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image from the checked out branch"
                    // Run the build script to build the Docker image
                    sh './build.sh'
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Login to Docker Hub using the credentials
                    echo "Logging into Docker Hub"
                    sh "docker login -u $DOCKER_USER -p $DOCKER_PASS"

                    // Get the branch name again to decide where to push the Docker image
                    def branch = env.GIT_BRANCH ? env.GIT_BRANCH.replace("origin/", "") : sh(script: "git rev-parse --abbrev-ref HEAD", returnStdout: true).trim()

                    if (branch == "main") {
                        echo "Pushing Docker image to 'prod' repository"
                        // Tag the image as prod and push to the prod repository
                        sh "docker tag $DOCKER_IMAGE_NAME_DEV $DOCKER_IMAGE_NAME_PROD"
                        sh "docker push $DOCKER_IMAGE_NAME_PROD"
                    } else {
                        echo "Pushing Docker image to 'dev' repository"
                        // Push the image to the dev repository
                        sh "docker push $DOCKER_IMAGE_NAME_DEV"
                    }
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                script {
                    echo "Deploying to the server"
                    // Run the deployment script to deploy the application
                    sh './deploy.sh'
                }
            }
        }
    }

    post {
        success {
            echo 'Build and deployment succeeded!'
        }
        failure {
            echo 'Build or deployment failed!'
        }
    }
}
