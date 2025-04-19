pipeline {
    agent {
        docker {
            image 'maven:3.9.4-eclipse-temurin-17'
            args '-v /var/run/docker.sock:/var/run/docker.sock'  // Mount Docker socket to the container
        }
    }

    environment {
        DOCKER_IMAGE = 'iamtanya28/java-app'  // Define the Docker image variable
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo '✅ Checkout Stage'
                // git branch: 'main', url: 'https://github.com/iamtanya28/java-app.git'
            }
        }

        stage('Build with Maven') {
            steps {
                echo '⚙️ Building with Maven'
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build & Push Docker Image') {
            environment {
                // Securely use the Jenkins credentials
                REGISTRY_CREDENTIALS = credentials('docker-cred')  // Replace with your Jenkins credentials ID
            }
            steps {
                script {
                    echo '🐳 Building Docker Image'
                    sh """
                        docker --version  # Verify Docker installation
                        docker build -t ${DOCKER_IMAGE} .
                    """
                    
                    // Login to Docker Hub using credentials
                    withCredentials([usernamePassword(credentialsId: 'docker-cred', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh """
                            echo "\$DOCKER_PASSWORD" | docker login -u "\$DOCKER_USERNAME" --password-stdin
                            docker push ${DOCKER_IMAGE}
                        """
                    }
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
