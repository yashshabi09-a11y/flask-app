pipeline {

    agent any

    environment {

        IMAGE_NAME = "yourdockerhub/flask-app"

    }

    stages {

        stage('Clone') {
            steps {
                git url: 'https://github.com/username/flask-app.git',
                branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

        stage('Login DockerHub') {

            steps {

                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {

                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    '''
                }

            }
        }

        stage('Push Image') {

            steps {

                sh '''
                docker push $IMAGE_NAME:$BUILD_NUMBER
                docker tag $IMAGE_NAME:$BUILD_NUMBER $IMAGE_NAME:latest
                docker push $IMAGE_NAME:latest
                '''
            }

        }

        stage('Deploy') {

            steps {

                sh '''
                docker stop flask || true

                docker rm flask || true

                docker pull $IMAGE_NAME:latest

                docker run -d \
                --name flask \
                -p 5000:5000 \
                $IMAGE_NAME:latest
                '''

            }

        }

    }

}
