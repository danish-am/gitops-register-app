pipeline {
    agent { label "Jenkins-Agent" }

    parameters {
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Docker image tag to deploy')
    }

    environment {
        APP_NAME = "register-app-pipeline"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/danish-am/gitops-register-app'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                    echo 'Before:'
                    cat deployment.yaml

                    sed -i 's|image: .*/.*|image: ${APP_NAME}:${IMAGE_TAG}|' deployment.yaml

                    echo 'After:'
                    cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                    git config --global user.name "danish-am"
                    git config --global user.email "ahmeddanish1729@gmail.com"
                    git add deployment.yaml
                    git commit -m "Updated Deployment Manifest with tag ${IMAGE_TAG}" || echo "No changes to commit"
                """
                withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
                    sh "git push https://${GIT_USER}:${GIT_PASS}@github.com/danish-am/gitops-register-app main"
                }
            }
        }
    }
}
