pipeline {
  agent {
    docker {
      image 'node:6-alpine'
      args '-p 3000:3000'
    }

  }
  stages {
    stage('build') {
      steps {
        sh '''npm install
'''
      }
    }
    stage('test') {
      steps {
        sh './jenkins/scripts/test.sh'
        echo 'hello'
      }
    }
    stage('deliver') {
      steps {
        script {
          def server = Artifactory.server 'artifactory'
          echo 'yo foo'
          workspace_path = System.getenv("BASE")
          echo workspace_path
          def uploadSpec = """{
            "files": [
              {
                "pattern": "jenkins/*",
                "target": "jenkins-blue-ocean-pipeline/"
              }
            ]
          }"""
          def buildInfo = server.upload(uploadSpec)
          buildInfo.env.capture = true
          server.publishBuildInfo buildInfo
        }

        sh './jenkins/scripts/deliver.sh'
        input 'Finished using the web site? (Click "Proceed" to continue)'
        sh './jenkins/scripts/kill.sh'
      }
    }
  }
  environment {
    CI = 'true'
  }
}