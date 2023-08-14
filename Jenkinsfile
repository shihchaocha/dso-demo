pipeline {
  agent {
    kubernetes {
      yamlFile 'build-agent.yaml'
      defaultContainer 'maven'
      idleMinutes 1
    }

  }
  stages {
    stage('Build') {
      parallel {
        stage('Compile') {
          steps {
            container(name: 'maven') {
              sh 'mvn compile'
            }

          }
        }

      }
    }

    stage('Test') {
      parallel {
        stage('Unit Tests') {
          steps {
            container(name: 'maven') {
              sh 'mvn test'
            }
          }
        }
      }
    }

    stage('Create Jarfile') {
      steps {
        container(name: 'maven') {
          sh 'mvn package -DskipTests'
        }
      }
    }

    stage('Deploy to Dev') {
      parallel {
        stage('Docker BnP') {
          steps {
            container(name: 'kaniko') {
              sh '''/kaniko/executor --verbosity debug -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --cache=true --destination=docker.io/shichoc/dso-demo:latest'''
            }
          }
        }
      }
    }
  }
}
