pipeline {
    agent any

    stages {

        stage('Create Network') {
            steps {
                sh '''
                docker network create lab-network || true
                '''
            }
        }

        stage('Build Backend Image') {
            steps {
                sh 'docker build -t backend-app backend'
            }
        }

        stage('Deploy Backends') {
            steps {
                sh '''
                docker rm -f backend1 backend2 || true
                docker run -d --network lab-network --name backend1 backend-app
                docker run -d --network lab-network --name backend2 backend-app
                '''
                sh 'sleep 3'
            }
        }

        stage('Start NGINX') {
            steps {
                sh '''
                docker rm -f nginx-lb || true
                docker run -d --network lab-network --name nginx-lb -p 80:80 nginx
                '''
                sh 'sleep 2'
            }
        }

        stage('Configure NGINX') {
            steps {
                sh '''
                docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf
                docker exec nginx-lb nginx -s reload
                '''
            }
        }
    }
}
