pipeline {
    agent {
        docker {
            image 'maven:3.8.7-eclipse-temurin-17' // has Maven + JDK
            args '-v /var/run/docker.sock:/var/run/docker.sock' // allows Docker commands from inside container
        }
    }

    environment {
        DOCKER_IMAGE = 'iamtanya28/java-app'
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

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKER_IMAGE ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh "docker push $DOCKER_IMAGE"
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
