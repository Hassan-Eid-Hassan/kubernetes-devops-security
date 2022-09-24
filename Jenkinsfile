pipeline {
    agent { label 'test' }
    tools {
        jdk 'jdk8'
    }
    stages {
        stage('Build JAR') {
            steps {
                  sh "mvn clean install -DskipTests=true"
            }
        }
        stage('Artifacts JAR') {
            steps {
                 archiveArtifacts artifacts: 'target/*.jar'
            }
        }
        stage('Unit Tests - JUnit and Jacoco') {
            steps {
                  sh "mvn test"
            }
        }
        stage('Mutation Tests PIT') {
            steps{ 
              sh "mvn org.pitest:pitest-maven:mutationCoverage" 
                 } 
        }
      //  stage('SonarQube - SAST') {
       //     steps {
        //          withSonarQubeEnv('SonerQube'){
        //          sh "mvn sonar:sonar -Dsonar.projectKey="----" -Dsonar.host.url=http://devsecops-demo.eastus.cloudapp.azure.com:9000"
    //}
             //    timeout(time: 2, unit: 'MINUTES'){
        //      script{
        //            waitForQualityGate abortPipeline: true
    //}
    //         }
         //   }
       // }
        stage('Vulnerability Scan Docker'){
            steps{
                sh "mvn dependency-check:check"
            }
        }
        stage('Docker image build and push'){
            steps {
                sh "docker build -t 192.168.205.130:5000/repository/hassan/java:${BUILD_NUMBER} ."
                sh "docker push 192.168.205.130:5000/repository/hassan/java:${BUILD_NUMBER}"
            }
        }
        stage('Kubernetes Deployment - DEV') {
            steps {
            sh "sed -i 's#REPLACE_ME#192.168.205.130:5000/repository/hassan/java:${BUILD_NUMBER}#g' k8s_deployment_service.yaml"
            sh "kubectl apply -f k8s_deployment_service.yaml"
            }
        }
   }
    post{
        always{
            junit 'target/surefire-reports/*.xml'
            jacoco execPattern: 'target/jacoco.exec'
            pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
            dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
        }
}
