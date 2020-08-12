pipeline {
  agent any
  environment {
    docker_username = "arian471"
  }
  stages {
    stage('__clone down__') {
      steps {
        
          stash excludes: '.git', name: 'code'
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
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            stash excludes: '.git', name: 'code'
          }
        }
      }
    }
    stage("push stuff"){
      environment{
        DOCKERCREDS = credentials("docker_login")
      }
        steps {
          unstash 'code' //unstash the repository code
          sh 'ci/build-docker.sh'
          sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin' //login to docker hub with the credentials above
          sh 'ci/push-docker.sh'
        }
    }
    stage("component test"){
      when { not { branch "dev/**" }}
      steps {
        unstash "code"
        sh "ci/component-test.sh"
      }
    }
  }
}