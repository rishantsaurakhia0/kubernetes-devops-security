pipeline {
  agent any

  stages {

    stage('Build Artifact - Maven') {
      steps {
        sh "mvn clean package -DskipTests=true"
        archive 'target/*.jar'
      }
    }

    stage('Unit Tests') {
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
    stage('SonarQube(SAST)') {
      steps {
          withSonarQubeEnv('SonarQube') {
          sh "mvn sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://devsecops1-demo.eastus.cloudapp.azure.com:9000 -Dsonar.login=53d261294f9fe3b4069e3dec24607604001d1063"
          }
      timeout(time: 2, unit: 'MINUTES') {
        script {
          waitForQualityGate abortPipeline: true
        }
      }
     }
    } 
    stage('OWASP Dependency Scan and Trivy scan') {
      steps {
        parallel(
          "Dependency Scan": {
          sh "mvn dependency-check:check"
      },
      "Trivy Scan": {
        sh "bash trivy-docker-image-scan.sh"
      },
      "OPA Conftest": {
        sh 'docker run --rm -v $(pwd):/project openpolicyagent/conftest test --policy opa-docker-security.rego Dockerfile'
      }
     )
    }  
   }
    stage('Docker Build and Push') {
      steps {
        withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
          sh 'printenv'
          sh 'sudo docker build -t rishw/numeric-app:""$GIT_COMMIT"" .'
          sh 'docker push rishw/numeric-app:""$GIT_COMMIT""'
        }
      }
    }

    stage('Vulnerability Scan - Kubernetes') {
      steps {
        sh 'docker run --rm -v $(pwd):/project openpolicyagent/conftest test --policy opa-k8s-security.rego k8s_deployment_service.yaml'
      }
    }

    stage('Kubernetes Deployment - DEVELOPMENT') {
      steps {
        withKubeConfig([credentialsId: 'kubeconfig']) {
          sh "sed -i 's#replace#rishw/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
          sh "kubectl apply -f k8s_deployment_service.yaml"
        }
      }
    }
    
  }

  post {
    always {
      junit 'target/surefire-reports/*.xml'
      jacoco execPattern: 'target/jacoco.exec'
      dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
    }
    // success {

    // }

    // failure {

    // }
   }

}