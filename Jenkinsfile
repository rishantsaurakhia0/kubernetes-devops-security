pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              echo "yes"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }   
    }
}
