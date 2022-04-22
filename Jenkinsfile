pipeline {
  agent none
  stages {
    stage('build') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        echo 'compiling sysfoo'
        sh 'mvn compile'
      }
    }

    stage('test') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        echo 'running unit tests'
        sh 'mvn clean test'
      }
    }

    stage('Package') {
        when {
                    branch "master"
        }
        parallel {
            stage('create Jarfile') {
                agent {
                    docker {
                        image 'maven:3.6.3-jdk-11-slim'
                    }
                }
                steps {
                    echo 'generating artifacts'
                    sh 'mvn package -DskipTests'
                    archiveArtifacts 'target/*.war'
                }
            }
            stage('Image BnP') {
              agent any
              steps {
                script {
                  docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                    def dockerImage = docker.build("grovervishal591/sysfoo:v${env.BUILD_ID}", "./")
                    dockerImage.push()
                    dockerImage.push("latest")
                    dockerImage.push("dev")
                  }
                }
              }
            }
        }

    }

  }
  tools {
    maven 'Maven 3.6.3'
  }
  post {
    always {
      echo 'This pipeline is completed..'
    }

  }
}
