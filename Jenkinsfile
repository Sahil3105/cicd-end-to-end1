pipeline {
    agent any 

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        
        stage('Checkout') {
            steps {
                git credentialsId: 'github-credentials-id', 
                    url: 'https://github.com/Sahil3105/cicd-end-to-end1.git',
                    branch: 'main'
            }
        }

        stage('Build Docker') {
            steps {
                script {
                    sh '''
                    echo Building Docker Image
                    docker build -t sahil3105/ciandcd:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-cred', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh '''
                        echo Pushing to Docker Hub
                        echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                        docker push sahil3105/ciandcd:${BUILD_NUMBER}
                        '''
                    }
                }
            }
        }
        
        stage('Update K8S manifest') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'github-credentials-id', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        echo Updating Kubernetes manifest
                        # Print the original deploy.yaml for debugging
                        echo "Original deploy.yaml:"
                        cat deploy/deploy.yaml
                        
                        # Perform the replacement
                        sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" deploy/deploy.yaml
                        
                        # Print the updated deploy.yaml for debugging
                        echo "Updated deploy.yaml:"
                        cat deploy/deploy.yaml
                        
                        # Configure Git with user details
                        git config user.email "jenkins@example.com"
                        git config user.name "Jenkins"
                        
                        # Add, commit, and push changes
                        git add deploy/deploy.yaml
                        git commit -m "Updated the deploy.yaml with build number ${BUILD_NUMBER}" || echo "No changes to commit"
                        
                        # Use credentials for the git push operation
                        git push https://$GIT_USERNAME:$GIT_PASSWORD@github.com/Sahil3105/cicd-end-to-end1.git HEAD:main
                        '''
                    }
                }
            }
        }
    }
}
