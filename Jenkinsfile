pipeline {
  agent {
    docker {
      image 'docker:24-git'                            // มี git ในตัว
      args "-u 0 -e HOME=/tmp -e DOCKER_CONFIG=/tmp/.docker -v /var/run/docker.sock:/var/run/docker.sock"
    }
  }
  options { timestamps(); skipDefaultCheckout(true) }

  stage('Checkout') {
  steps {
    git branch: 'main',
        url: 'https://github.com/Navaphon001/jenkins-demo-app.git'
  }
}


    stage('Build Image') {
      steps {
        sh '''
          export DOCKER_BUILDKIT=1
          docker version
          docker build --pull -t jenkins-demo-app:latest .
        '''
      }
    }

    stage('Run Container') {
      steps {
        sh '''
          docker rm -f demo-app 2>/dev/null || true
          docker run -d --name demo-app -p 8081:8081 jenkins-demo-app:latest
        '''
      }
    }

  post {
    always {
      sh 'docker ps --format "table {{.Names}}\\t{{.Image}}\\t{{.Ports}}" || true'
    }
  }
}
