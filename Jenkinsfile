pipeline {
    agent any
    options {
        timeout(time: 20, unit: 'MINUTES')
        disableConcurrentBuilds()
    }
    environment {
        IMAGE = "lab-midterm:latest"
        CONTAINER = "lab-midterm"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build image (trains model)') {
            steps {
                sh 'docker build -t $IMAGE .'
            }
        }
        stage('Restart container') {
            steps {
                sh '''
                  docker rm -f $CONTAINER 2>/dev/null || true
                  docker run -d \
                    --name $CONTAINER \
                    --restart unless-stopped \
                    -p 8000:8000 \
                    $IMAGE
                '''
            }
        }
        stage('Health check') {
            steps {
                sh '''
                  sleep 4
                  curl -sf http://localhost:8000/metrics
                  echo
                '''
            }
        }
    }
}
