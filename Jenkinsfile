pipeline {
  agent any
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
        withCredentials([usernamePassword(credentialsId: '5e322410-00af-4ee3-b2ff-6bf5ffd0f194', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
          script {
              sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker pull richardluo0506/test:latest\""
              try {
                  sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker stop test\""
                  sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker rm test\""
              } catch (err) {
                  echo: 'caught error: $err'
              }
              sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker run --restart always --name test -p 8000:8000 -d richardluo0506/test:latest\""
          }
        }
      }
    }
  }
}