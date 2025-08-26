pipeline {
  agent {
    docker {
      image 'docker:24-git'                            // มี git ในตัว
      args "-u 0 -e HOME=/tmp -e DOCKER_CONFIG=/tmp/.docker -v /var/run/docker.sock:/var/run/docker.sock"
    }
  }
  options { timestamps(); skipDefaultCheckout(true) }

  stages {
    stage('Checkout') {
      steps {
        checkout scm                                     // ใช้ Jenkinsfile จาก repo เดิม
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
          docker run -d --name demo-app -p 5000:5000 jenkins-demo-app:latest
        '''
      }
    }
  }

  post {
    always {
      sh 'docker ps --format "table {{.Names}}\\t{{.Image}}\\t{{.Ports}}" || true'
    }
  }
}
