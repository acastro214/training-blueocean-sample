pipeline {
  agent {
    docker {
      image 'bitwiseman/training-blueocean-sample'
      args '-u root -v $HOME/.m2:/root/.m2'
    }
    
  }
  stages {
    stage('Build') {
      steps {
        sh './jenkins/build.sh'
        archiveArtifacts 'target/*.war'
      }
    }
    stage('Test') {
      steps {
        parallel(
          "Test": {
            sh './jenkins/test-all.sh'
            junit '**/surefire-reports/**/*.xml'
            junit '**/test-results/karma/*.xml'
            
          },
          "Backend": {
            sh './jenkins/test-backend.sh'
            
          },
          "Static": {
            sh './jenkins/test-static.sh'
            
          },
          "Performance": {
            sh './jenkins/test-performance.sh'
            
          }
        )
      }
    }
    stage('Deploy to Dev') {
      steps {
        sh './jenkins/deploy.sh dev'
      }
    }
    stage('Deploy to Staging') {
      steps {
        input(message: 'Deploy to staging?', ok: 'Fire away!')
        sh './jenkins/deploy.sh staging'
        sh 'echo Notifying appropriate team members!'
      }
    }
  }
}