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
               kubeconfig(caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMvakNDQWVhZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJeU1Ea3lNekUyTWpBeE1Gb1hEVE15TURreU1ERTJNakF4TUZvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTnJGCnkwQlc4cGk0UXpST1AveTJPY0t2bWtUcURkNXBuMzBRUWZRYzNFdDRrVUU3RHk5Nk9RcTdrT2twNEwyNEs0UEoKTXp1MXdNb1JzbXlZcWpPYW4yZnlMRkpmbnYyK09QQlVKU0Q3QWh0WVErMkRWZ0xWdEJQdmFaWEljRUJIdmNtdgpVVVhGSEMxSTN5bFA4Nkp2ZklWYkFWSDN6QlM2R3dIOU8xL0kvMWhGUmVQdmp1SUFxdW5obUJGNVJ0M08yZjRaCmg4S3YzOVFHZkE5cW1VWndEV3F6WHdIZ3FvV00zWG5HeS9kajJ5cklmeDhleUV3T1hxbUVvWUtXTGFMUStROEYKRGFKUXN0M0hBc0E5SE5pU2JyU1cyMkdHMTc2UTF4QitJcEZTc002YlNsV0pUamhlN3hrclFwbm91TVVhcjl1Tgp0UFQrWkh2LzBmNnlHZ1dsU2prQ0F3RUFBYU5aTUZjd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0hRWURWUjBPQkJZRUZFYm1YdFFRU0kzMFk1SWNrR1A5RmdHWkFoa2tNQlVHQTFVZEVRUU8KTUF5Q0NtdDFZbVZ5Ym1WMFpYTXdEUVlKS29aSWh2Y05BUUVMQlFBRGdnRUJBR3dtTlhBK1lnT3JXWFdkeGdlRQpjc3JNNzFhdnMvYmxOWjlyRURhUkM3U245OE0rUllDRFJ4MHB0RFFRL0lLdHB4WkkzYzhrSjl3VjdIU0xrbWdsClNNN0J5aGxDcU4zaWNCZTY4dTBjclgvTHVWUEhvM3BpcVo2Wnp0MEZQZ3lnQXppWk43RFFDVzY0U1ljMnM1cHQKaStSeGYzdjRUL210MTVNSkNmbWoyT2R1ay9WQmI5MVdWNG1VSnpzZjZLM3I5SVUraE1pbHc4MFRQeXQyZVlnZQpzKzlEUC95a3JPc3QwZCtTMlJjaS9FRmJCaEN3Um5NbGJod3FZTU12QUd2M1RQWjlRbmcwWGtuRURYVXNwMHpDCjQ4Z0NVTzZzc29tUitHckhSYmp0TE5OcTZFNmhCcmUvWmMrZlRMaVBBSmVZSDV5d210SGwrYWxydFlCMTZRek0KbkFFPQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==', credentialsId: 'kubeconfig', serverUrl: 'https://192.168.205.133:6443') {
                sh "sed -i 's#REPLACE_ME#192.168.205.130:5000/repository/hassan/java:${BUILD_NUMBER}#g' k8s_deployment_service.yaml"
                sh "kubectl apply -f k8s_deployment_service.yaml"
               }
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
}
