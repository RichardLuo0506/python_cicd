pipeline {
  agent any
  environment {
    USERNAME = 'ubuntu'
    SERVER = '172.31.63.120'
  }

  stages {
    stage ('Build Docker Image') {
      when {
        branch 'main'
      }
      steps {
        script {
          app = docker.build('richardluo0506/test')
        }
      }
    }
    stage('Push Docker Image') {
      when {
        branch 'main'
      }
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            app.push("latest")
          }
        }
      }
    }
    stage('Deploy to prod') {
      when {
        branch 'main'
      }
      steps {
        input 'Deploy to production?'
        milestone(1)
        sshagent (credentials: ['5e322410-00af-4ee3-b2ff-6bf5ffd0f194']) {
          script {
            sh "ssh $USERNAME@$SERVER docker ps"
          }
        }
      }
    }
  }
}