pipeline {
    agent any

    environment {
        DOCKER_REPO = 'abdullah4665/ml-project'
        VERSION = "0.0.${BUILD_NUMBER}"
        GIT_BRANCH = 'master'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Pulling code from Git repository...'
                git branch: "${GIT_BRANCH}", url: 'https://github.com/AbdullahSoftech/flask_web_app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh """
                docker build -t ${DOCKER_REPO}:${VERSION} .
                """
            }
        }

        stage('Push Docker Image') {
            steps {
                echo 'Pushing Docker image to DockerHub...'
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh """
                    echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                    docker push ${DOCKER_REPO}:${VERSION}
                    """
                }
            }
        }

        stage('Tag New Version') {
            steps {
                echo 'Tagging new version...'
                withCredentials([usernamePassword(credentialsId: 'github-creds', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_TOKEN')]) {
                    sh """
                    git config --global user.name "$GITHUB_USERNAME"
                    git config --global user.email "$GITHUB_USERNAME@gmail.com"
                    git tag v${VERSION}
                    git push https://$GITHUB_USERNAME:$GITHUB_TOKEN@github.com/AbdullahSoftech/flask_web_app.git v${VERSION}
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
    }
}