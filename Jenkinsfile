pipeline {
    agent any

    options {
        disableConcurrentBuilds()  //Prevents concurrent builds of the same job 
    }

    environment {
        IMAGE_NAME = "parte15/ecommerce-flask-app"
        IMAGE_TAG = "build-${env.BUILD_NUMBER}"
        GIT_USER   = "15Vaibhavparte"
        GIT_EMAIL  = "vaibhavparte2@gmail.com"
    }

    stages {
        
        stage('cleanup') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Push Image') {
            when { branch 'main' }
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {
                        sh """
                        docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                        """
                    }
                }
            }
        }

        stage('Update K8s Manifest') {
            when { branch 'main' }
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'github-creds',
                        usernameVariable: 'GIT_USERNAME',
                        passwordVariable: 'GIT_TOKEN'
                    )]) {
                        sh """
                        set -e
                        git config user.name "$GIT_USER"
                        git config user.email "$GIT_EMAIL"

                        git fetch origin
                        git checkout main
                        git reset --hard origin/main

                        sed -i "s|image:.*|image: ${IMAGE_NAME}:${IMAGE_TAG}|" k8s/deployment.yml

                        git add k8s/deployment.yml
                        git diff --cached --quiet || git commit -m "Updated image to ${IMAGE_TAG}"
                        git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/15Vaibhavparte/ecommerce-gitops-pipeline.git main
                
                        """
                    }
                }
            }
        }
    }
}
