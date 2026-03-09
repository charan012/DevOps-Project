pipeline {
    agent slavenode
    
    environment {
        DOCKER_USERNAME = 'charan012'
        DOCKER_REPO = 'devops-repo'
        DOCKER_IMAGE = "${charan012}/${devops-repo}:${BUILD_NUMBER}"
        DOCKER_IMAGE_LATEST = "${charan012}/${devops-repo}:latest"
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo "Checking out code..."
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo "Building with Maven..."
                sh 'mvn clean install -DskipTests=true'
            }
        }
        
        stage('Docker Build') {
            steps {
                echo "Building Docker image..."
                sh 'docker build -t ${DOCKER_IMAGE} .'
                sh 'docker tag ${DOCKER_IMAGE} ${DOCKER_IMAGE_LATEST}'
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                echo "Pushing to Docker Hub..."
                sh '''
                    echo $DOCKER_CREDENTIALS_PSW | docker login -u $DOCKER_CREDENTIALS_USR --password-stdin
                    docker push ${DOCKER_IMAGE}
                    docker push ${DOCKER_IMAGE_LATEST}
                    docker logout
                '''
            }
        }
        
        stage('Deploy') {
            steps {
                echo "Deploying container..."
                sh '''
                    docker stop dev-web-app || true
                    docker rm dev-web-app || true
                    docker run -itd -p 8081:8080 --name dev-web-app ${DOCKER_IMAGE_LATEST}
                    sleep 3
                    docker ps
                '''
            }
        }
    }
    
    post {
        always {
            echo "Pipeline completed: ${currentBuild.result}"
        }
    }
}
