pipeline {
    agent any

    environment {
        IMAGE_NAME = "purvaawankhede/node-staging-app"
        CONTAINER_NAME = "staging-node-app"
    }

    stages {
       stage('Checkout Code') {
           steps {
               git branch: 'main',
                   url: 'https://github.com/purvaa01/automate_deployment.git'
           }
       }


        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }


        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh '''
                      echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                      docker push $IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                sh '''
                  docker rm -f $CONTAINER_NAME || true
                  docker run -d \
                    --name $CONTAINER_NAME \
                    -p 8090:3000 \
                    $IMAGE_NAME:latest
                '''
            }
        }
    }
}
