pipeline {
    agent any 

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        
        stage('Checkout'){
            steps {
                git credentialsId: 'new-git-credentials-id', 
                    url: 'https://github.com/Sahil3105/cicd-end-to-end1.git',
                    branch: 'main'
            }
        }

        stage('Build Docker'){
            steps {
                script {
                    sh '''
                    echo 'Building Docker Image'
                    docker build -t sahil3105/ciandcd:${IMAGE_TAG} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '''
                        echo 'Pushing to Docker Hub'
                        echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                        docker push sahil3105/ciandcd:${IMAGE_TAG}
                        '''
                    }
                }
            }
        }
        
        stage('Update K8S manifest'){
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'new-git-credentials-id', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        echo 'Updating Kubernetes manifest'
                        sed -i "s/32/${BUILD_NUMBER}/g" deploy/deploy.yaml
                        cat deploy/deploy.yaml
                        git add deploy/deploy.yaml
                        git commit -m 'Updated the deploy.yaml with build number ${BUILD_NUMBER}'
                        git remote -v
                        git push https://github.com/iam-veeramalla/cicd-demo-manifests-repo.git HEAD:main
                        '''
                    }
                }
            }
        }
    }
}
