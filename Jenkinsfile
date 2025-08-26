pipeline {
  agent {
    docker {
      image 'docker:20.10.7'
      args "--entrypoint='' -v /var/run/docker.sock:/var/run/docker.sock"
    }
  }
  options { skipDefaultCheckout(true) }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Image') {
      steps {
        sh 'docker version && docker build -t jenkins-demo-app:latest .'
      }
    }

    stage('Run Container') {
      steps {
        sh '''
          docker rm -f demo-app  true
          docker run -d --name demo-app -p 5000:5000 jenkins-demo-app:latest
        '''
      }
    }
  }

  post {
    always {
      sh 'docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Ports}}"  true'
    }
  }
}