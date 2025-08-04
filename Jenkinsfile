pipeline {
    agent any

    environment {
        IMAGE_NAME = "villa-agency-site"
        DOCKER_USER = "hash321"
        CONTAINER_NAME = "villa-agency"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    writeFile file: 'Dockerfil
                        FROM nginx:alpine
                        COPY . /usr/share/nginx/html
                        EXPOSE 80
                    '''
                }
                sh 'docker build -t $DOCKER_USER/$IMAGE_NAME .'
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'docker-token', variable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $DOCKER_USER/$IMAGE_NAME
                    '''
                }
            }
        }

        stage('Deploy on EC2 (Same Instance)') {
            steps {
                sh '''
                    docker rm -f $CONTAINER_NAME || true
                    docker pull $DOCKER_USER/$IMAGE_NAME
                    docker run -d -p 80:80 --name $CONTAINER_NAME $DOCKER_USER/$IMAGE_NAME
                '''
            }
        }
    }
}

