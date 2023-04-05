pipeline{
    agent any

    tools {
         maven 'MAVEN_HOME'
         jdk 'JAVA_HOME'
    }

    stages{
        stage('Git Checkout'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'github access', url: 'https://github.com/GoudSagar/Hello-World-Code.git']]])
            }
        }
        stage('build'){
            steps{
               sh '''
                mvn package
                '''
            }
        }
        stage ('Unit Test') {
	        steps {
                echo 'Running Unit Testing'
                sh '''
                mvn test
                '''
             }
         }
  
        stage ('Static Code Analysis') {
             environment {
             scannerHome = tool 'SONAR_SCANNER'
             }
             steps {
                echo 'Running Static Code Analysis'
                 withSonarQubeEnv('SONAR_HOME') {
                 sh '${scannerHome}/bin/sonar-scanner'
                 }
            }
        }
        
            stage ('Artifact Deploy to Nexus') {
             steps {
                   nexusArtifactUploader(
                   nexusVersion: 'nexus3',
                   protocol: 'http',
                   nexusUrl: '18.117.164.89:8081',
                   groupId: 'MY-POC',
                   version: '1.0-SNAPSHOT',
                   repository: 'maven-snapshots',
                   credentialsId: 'nexus-pass',
                       artifacts: [
                         [artifactId: 'POC-CI-CD',
                         type: 'war',
                         classifier: '',
                         file: 'webapp/target/webapp.war']
                       ]
                   )
               }
          }
        stage ('Tomcat Deployment') {
           steps {
             script {
                 deploy adapters: [tomcat9(credentialsId: 'tomcat-pass', path: '', url: 'http://18.118.167.237:8080')], contextPath: '/pipeline', onFailure: false, war: 'webapp/target/webapp.war' 
                    }
                  }
           }
    
     }
}
