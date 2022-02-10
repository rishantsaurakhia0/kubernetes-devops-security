pipeline {
  agent any

  stages {
<<<<<<< HEAD
    stage('Build Artifact - Maven') {
      steps {
        sh "mvn clean package -DskipTests=true"
        archive 'target/*.jar'
      }
    }

    stage('Unit Tests - JUnit and Jacoco') {
      steps {
        sh "mvn test"
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
          jacoco execPattern: 'target/jacoco.exec'
        }
      }
    }
  }
=======
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              echo "yes"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }   
    }
>>>>>>> ab83dd4713fb3c14e64c9e4de410fd0b1faf4739
}
