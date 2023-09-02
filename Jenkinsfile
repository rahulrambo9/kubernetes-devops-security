pipeline {
  agent any
  stages {
      stage('Build Artifact') {
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

      stage('Docker build and push') {
            steps {
              withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
                sh 'printenv'
                sh 'docker build -t rahulrambo9/numeric-app-devsecop:""$GIT_COMMIT"" .'
                sh 'docker push rahulrambo9/numeric-app-devsecop:""$GIT_COMMIT""'
              }  
            }
        }
      stage('K8 deployment - DEV') {
            steps {
              withKubeConfig([credentialsId: 'kubeconfig']) {
                sh "sed -i 's#replace#rahulrambo9/numeric-app-devsecop:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
                sh "kubectl apply -f k8s_deployment_service.yaml"
                
              }  
            }
        }     
    }
}  