pipeline {
  agent any
  stages {
    stage('Unstash'){
      steps {
        unstash 'code'
      }
    }
    stage('Say Hello') {
      parallel {
        stage('Parallel execution') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('build app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          options {
            skipDefaultCheckout(true)
          }
          steps {
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
          }
        }
      }
    }

    
    stage('__clone down__') {
      steps {
        
          stash excludes: '.git', name: 'code'
      }
    }

  }
}