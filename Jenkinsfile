pipeline {
  agent {
    docker {
      image 'docker:24-git'
      args "--entrypoint='' -u 0 -e HOME=/tmp -e DOCKER_CONFIG=/tmp/.docker -v /var/run/docker.sock:/var/run/docker.sock"
    }
  }

  options { timestamps(); skipDefaultCheckout(true) }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main',
            url: 'https://github.com/Navaphon001/jenkins-demo-app.git'
        // เก็บซอร์สไว้ใช้ข้าม agent
        stash name: 'src', includes: '**/*'
      }
    }

    stage('Test') {
      // ใช้คอนเทนเนอร์ Python แยกต่างหาก
      agent {
        docker {
          image 'python:3.9-slim'
          args "-u 0"
        }
      }
      steps {
        // ดึงซอร์สที่ stash ไว้มาใช้ใน workspace ของ agent นี้
        unstash 'src'
        sh '''
          pip install --no-cache-dir -r requirements.txt pytest
          pytest -q --junitxml=test-results.xml
        '''
      }
      post {
        always { junit 'test-results.xml' }
      }
    }

    stage('Build Image') {
      steps {
        // เผื่อกรณีมี stage ก่อนหน้าใช้ agent อื่น ให้ดึงซอร์สกลับมาเสมอ
        unstash 'src'
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
  }

  post {
    always {
      sh 'docker ps --format "table {{.Names}}\\t{{.Image}}\\t{{.Ports}}" || true'
    }
  }
}
