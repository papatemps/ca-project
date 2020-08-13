pipeline {   
   environment {
    docker_username = 'mathn16'
  }
  agent any
  stages {
    stage('Dockerrize app') {
      parallel {
        stage('Dockerrize app') {
          steps {
            sh './build_script.sh'
            stash name: 'code', excludes:'.git'
          }
        }

        stage('Artifacts') {
          steps {
            sh './build_zip.sh'
            archiveArtifacts artifacts: 'ca-build.zip'
          }
        }
      }
    }
    stage('Unit testing'){
      agent {
        label 'test'
        docker {
          image 'python:3'
        }
      }
      steps{
        unstash 'code'
        sh './run_tests.sh'
      }
    }
    stage('DockerHub push') {
      environment{
        DOCKERCREDS = credentials('docker_login')
      }
      when {
        anyOf{
          branch 'master';
          changeRequest()
        }
      }
      steps {
        unstash 'code'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin'
        sh './push_to_hub.sh'
      }
    }
  }
}
