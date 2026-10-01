pipeline {

    agent any

    stages {

        stage('Clone GitHub') {
            steps {
                echo 'Cloning Omnifood project...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t omnifood:latest .'
            }
        }

        stage('Test Docker Image') {
            steps {
                echo 'Testing Docker image...'

                sh '''
                    docker rm -f omnifood-test || true
                    docker run -d --name omnifood-test -p 8090:80 omnifood:latest
                    sleep 5
                    curl -f http://localhost:8090
                    docker rm -f omnifood-test
                '''
            }
        }

        stage('Deploy to AWS EC2') {
            steps {
                echo 'Deploying Omnifood...'

                sh '''
                    docker rm -f omnifood-container || true
                    docker run -d \
                      --name omnifood-container \
                      --restart unless-stopped \
                      -p 80:80 \
                      omnifood:latest
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Checking website...'

                sh '''
                    sleep 5
                    curl -f http://localhost
                    docker ps
                '''
            }
        }
    }

    post {

        success {
            echo 'Omnifood deployment successful!'
        }

        failure {
            echo 'Omnifood deployment failed!'
        }
    }
}
