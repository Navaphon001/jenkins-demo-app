pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Navaphon001/jenkins-demo-app'
            }
        }
        stage('Build Image') {
            steps {
                sh 'docker build -t jenkins-demo-app:latest .'
            }
        }
        stage('Run Container') {
            steps {
                sh 'docker run -d -p 5000:5000 --name demo-app jenkins-demo-app:latest'
            }
        }
    }
}

