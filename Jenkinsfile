pipeline {
    agent {
        docker {
            image 'maven:3.9.4-eclipse-temurin-17'
             args '-v /var/run/docker.sock:/var/run/docker.sock'  
        }
    }

    
    stages {
        stage('Checkout') {
            steps {
                sh 'echo passed'
                // git branch: 'main', url: 'https://github.com/iamtanya28/java-app.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
stage('Build & Push Docker Image') {
            environment {
                REGISTRY_CREDENTIALS = credentials('docker-cred') // replace with your ID
            }
            steps {
                script {
                    echo '🐳 Building Docker Image'
                    sh """
                        docker --version  # Check Docker version to verify it's available
                        docker build -t $DOCKER_IMAGE .
                        echo "$REGISTRY_CREDENTIALS_PSW" | docker login -u "$REGISTRY_CREDENTIALS_USR" --password-stdin
                        docker push $DOCKER_IMAGE
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully!!'
        }
        failure {
            echo '❌ Pipeline failed!!'
        }
    }
}
