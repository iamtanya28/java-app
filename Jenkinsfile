pipeline {
    agent {
        docker {
            image 'iamtanya28/java-app:latest'
            args '-v /var/run/docker.sock:/var/run/docker.sock' // allows Docker commands from inside container
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

        stage('Build & push Docker Image') {
            environment {
        DOCKER_IMAGE = 'iamtanya28/java-app'
        REGISTRY_CREDENTIALS = credentials('docker-cred')

    }
            steps {
    script{
                sh "docker build -t $DOCKER_IMAGE ."
        def dockerImage = docker.image("${DOCKER_IMAGE}")
            docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                dockerImage.push()
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
