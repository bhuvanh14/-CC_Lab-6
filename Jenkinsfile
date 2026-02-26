pipeline {
    agent any

    stages {

        stage('Create Docker Network') {
            steps {
                sh '''
                docker network create lab-net || true
                '''
            }
        }

        stage('Build Backend Image') {
            steps {
                sh '''
                docker rmi -f backend-app || true
                docker build -t backend-app backend
                '''
            }
        }

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                docker rm -f backend1 backend2 || true

                docker run -d --network lab-net --name backend1 backend-app
                docker run -d --network lab-net --name backend2 backend-app

                sleep 5
                '''
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                docker rm -f nginx-lb || true

                docker run -d --network lab-net --name nginx-lb -p 80:80 nginx:latest

                sleep 3

                docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf
                docker exec nginx-lb nginx -s reload
                '''
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed. Check console logs for errors.'
        }
    }
}
