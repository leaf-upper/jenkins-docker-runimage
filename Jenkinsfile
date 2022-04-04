pipeline {
  agent any

  parameters {
    text(name: 'KARATE_TEST_CURL', description: 'Karate 테스트 CURL')
  }

  environment {
    def GIT_URL = "https://github.com/UpperLeaf/jenkins-docker-runimage.git"
    def BUILD_VERSION = sh(script: "echo `date +%Y%m%d%H%M%S`", returnStdout: true).trim()
  }

  stages {
    stage ('checkout') {

      steps {
        dir('demo-project') {
          git branch : 'master', url : "${GIT_URL}"
        }
      }
    }

    stage('build') {
      steps {
        dir('demo-project') {
          sh './gradlew build'
        }
      }
    }

    stage('build image') {
      steps {
        dir('demo-project') {
          script {
            app = docker.build('leafupper/demo-project')
          }
        }
      }
    }

    stage('ECHO URL') {
      steps {
        sh 'echo ${env.JENKINS_URL}'
        sh 'echo ${env.JOB_URL}'
        sh 'echo ${env.BUILD_URL}'
        sh 'echo ${env.PROJECT_URL}'
      }
    }

    stage('test image') {
      steps {
        dir('demo-project') {
          script {
            app.withRun('-p 8081:8080') { c ->
              sh KARATE_TEST_CURL
            }
          }
        }
      }
    }

    stage('publish image') {
      steps {
        dir('demo-project') {
          script {
            app.push("leaf-${BUILD_VERSION}")
          }
        }
      }
    }
  }

}
