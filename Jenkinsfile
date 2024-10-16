pipeline {
  agent any
  stages {
    stage('build') {
      agent {
        docker {
          image 'maven:3.9.6-eclipse-temurin-17'
        }

      }
      steps {
        echo 'compile maven app'
        sh 'mvn compile'
      }
    }

    stage('test') {
      agent {
        docker {
          image 'maven:3.9.6-eclipse-temurin-17'
        }

      }
      steps {
        echo 'test maven app'
        sh 'mvn clean test'
      }
    }

    stage('package') {
      when {
        branch 'main'
      }
      parallel {
        stage('package') {
          agent {
            docker {
              image 'maven:3.9.6-eclipse-temurin-17'
            }

          }
          steps {
            echo 'package maven app'
            sh 'mvn package -DskipTests'
          }
        }

        stage('DockerB&P') {
          steps {
            script {
              docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                def commitHash = env.GIT_COMMIT.take(7)
                def dockerImage = docker.build("kaushal2712/sysfoo:${commitHash}", "./")
                dockerImage.push()
                dockerImage.push("latest")
                dockerImage.push("dev")
              }
            }

          }
        }

      }
    }

    stage('deploy to dev') {
      when {
        branch 'main'
      }
      steps {
        echo 'deploying to dev'
        sh 'docker compose up -d'
      }
    }

  }
  tools {
    maven 'Maven 3.6.3'
  }
}
