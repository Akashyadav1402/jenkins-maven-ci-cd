pipeline {
    agent any

    environment {
        MAVEN_HOME = tool 'maven3'
        SONARQUBE_SERVER = 'sonarqube'
        NEXUS_CREDENTIALS = credentials('nexus')
        TOMCAT_CREDENTIALS = credentials('tomcat-creds')
        TOMCAT_URL = 'http://tomcat-host:9080/manager/text'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Akashyadav1402/jenkins-maven-ci-cd.git', branch: 'main'
            }
        }

        stage('Code Quality - SonarQube') {
            steps {
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    sh "${MAVEN_HOME}/bin/mvn clean verify sonar:sonar"
                }
            }
        }

        stage('Build') {
            steps {
            sh "${MAVEN_HOME}/bin/mvn clean package -DskipTests"
            }
        }

        stage('Upload to Nexus') {
            steps {
        withCredentials([usernamePassword(credentialsId: 'nexus', usernameVariable: 'admin', passwordVariable: 'admin')]) {
            sh """
                curl -v -u $admin:$admin \
                --upload-file target/myapp-1.0.0.war \
                http://localhost:8081/repository/maven-releases/com/example/myapp/1.0.0/myapp-1.0.0.war
            """
        }
    }
}

            
            }
        }

     stage('Deploy to Tomcat'){
        steps {
        withCredentials([usernamePassword(credentialsId: 'tomcat-creds', usernameVariable: 'jenkins', passwordVariable: 'jenkins')]) 
        {
          sh "curl -v --upload-file target/myapp-1.0.0.war '${TOMCAT_HOST}/manager/text/deploy?path=/myapp&update=true' --user $TOMCAT_USER:$TOMCAT_PASS"
        }     
        }
         }

    post {
        success {
            echo '🎉 Deployment Successful!'
        }
        failure {
            echo '❌ Build or Deployment Failed!'
        }
    } 
