pipeline {
  agent any  // This specifies the pipeline will run on any available Jenkins agent

  triggers {
    githubPush()  // Ensures the pipeline triggers automatically on GitHub push events
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/Madhu-123-bot/wed-proj-1.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          dockerImage = docker.build("containerguru1/wed-proj-1")
        }
      }
    }

    stage('Push Docker Image to Docker Hub') {
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'doc-hub-cred') {
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploy to AWS EC2') {
      steps {
        script {
          // Ensure permissions are correct for the private key
          sh 'chmod 600 /var/lib/jenkins/workspace/Jenkins-proj/ssh8633610058472328627.key'

          // Set the path to Ansible in case Jenkins can't find it
          withEnv(["PATH+ANSIBLE=/usr/local/bin:/usr/bin"]) {
            // Use sh step to run ansible-playbook command
            sh '''
              ansible-playbook playbook.yml -i hosts.txt --private-key /var/lib/jenkins/workspace/Jenkins-proj/ssh8633610058472328627.key -u ubuntu
            '''
          }
        }
      }
    }
  }

  post {
    always {
      script {
        try {
          // Remove Docker image
          docker.image('containerguru1/wed-proj-1').remove()
        } catch (Exception e) {
          echo "Image cleanup failed: ${e.getMessage()}"
        }
      }
    }
    failure {
      echo "Pipeline failed!"
    }
    success {
      echo "Pipeline completed successfully!"
    }
  }
}
