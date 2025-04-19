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

        stage('Install Docker') {
            steps {
                echo '🔧 Installing Docker'
                sh '''
                    apt-get update
                    apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release
                    curl -fsSL https://download.docker.com/linux/debian/gpg | tee /etc/apt/trusted.gpg.d/docker.asc
                    echo "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list
                    apt-get update
                    apt-get install -y docker-ce docker-ce-cli containerd.io
                '''
            }
        }

        stage('Build & Push Docker Image') {
            environment {
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
