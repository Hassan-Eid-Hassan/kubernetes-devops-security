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
        stage('Test') {
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
        stage('Mutation Tests PIT') {
            steps{ 
              sh "mvn org.pitest:pitest-maven:mutationCoverage" 
                 } 
            post { 
                always { 
                    pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml' 
                        }
                    }   
        }
      //  stage('SonarQube - SAST') {
       //     steps {
        //          sh "mvn sonar:sonar -Dsonar.projectKey="----" -Dsonar.host.url=http://devsecops-demo.eastus.cloudapp.azure.com:9000 -Dsonar.login="-----""
         //   }
       // }
        stage('Build Image'){
            steps {
                sh "docker build -t 192.168.205.130:5000/repository/hassan/java:${BUILD_NUMBER} ."
                sh "docker push 192.168.205.130:5000/repository/hassan/java:${BUILD_NUMBER}"
            }
                
        }
        //stage('Scan') {
          //  steps {
          //      sh "docker scan hassaneid/java:${BUILD_NUMBER} >> scanresult.txt | set +e"
        //
        //    }
        //}
       // stage('Publish') {
         //   steps {
           //    sh 'docker push hassaneid/java:${BUILD_NUMBER}'
           // }
       // }
       // stage('Deploy'){
         //   steps {
           //     sh """ ssh -i /home/ec2-user/eand.pem ec2-user@52.91.25.97 'sudo kubectl set image deployment java-deployment java=hassaneid/java:${BUILD_NUMBER}' """
           // } 
       // }

    }
}
