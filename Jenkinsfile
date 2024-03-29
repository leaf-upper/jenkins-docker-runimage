pipeline {
  agent any

  environment {
    def GIT_URL = "https://github.com/UpperLeaf/jenkins-docker-runimage.git"
    def BUILD_VERSION = sh(script: "echo `date +%Y%m%d%H%M%S`", returnStdout: true).trim()
    def HOST = getHostIp()
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

    stage('debug') {
      steps {
        sh "echo ${getHostIp()}"
        sh "echo ${TEST_PORT}"
      }
    }

    stage('test image') {
      steps {
        dir('demo-project') {
          script {
            app.withRun("-p ${TEST_PORT}:8080") { c ->
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

def getHostIp() {
    return env.JENKINS_URL.split('/')[2].split(':')[0]
}
