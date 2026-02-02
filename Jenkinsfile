pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/youruser/yourrepo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t monitored-app:latest app'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                if [ "$(docker ps -q -f name=monitored-app)" ]; then
                  docker stop monitored-app
                  docker rm monitored-app
                fi
                docker run -d --name monitored-app -p 8000:8000 monitored-app:latest
                '''
            }
        }
    }
}
