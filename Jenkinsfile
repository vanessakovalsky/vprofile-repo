pipeline {
    agent {
        docker {
            image 'maven:3.9.6-eclipse-temurin-17-alpine'
            args '-v /root/.m2:/root/.m2' 
        }
    }
    stages{
        stage('fetch code') {
          steps{
              git branch: 'vp-rem', url: "https://github.com/devopshydclub/vprofile-repo.git"
          }  
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo "Now Archiving."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage('Test'){
            steps {
                sh 'mvn test'
            }
          post {
            always {
              junit(testResults: 'target/surefire-reports/*.xml', allowEmptyResults : true)
            }
          }
        }
         stage('Code Analysis'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                    always {
                        recordIssues enabledForFailure: true, tools: checkStyle(pattern: '**/target/checkstyle-result.xml')
                    }
        }

        }
        stage("UploadArtifact"){
            steps{
                nexusArtifactUploader(
                  nexusVersion: 'nexus3',
                  protocol: 'http',
                  nexusUrl: 'adt-nexus.easyvista-training.com:8081',
                  groupId: 'vanessa-group-maven',
                  version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                  repository: 'vanessa-maven-hosted',
                  credentialsId: 'nexus-login',
                  artifacts: [
                    [artifactId: 'vproapp',
                     classifier: '',
                     file: 'target/vprofile-v2.war',
                     type: 'war']
                  ]
             )
        }      
    }
}
}
