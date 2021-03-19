pipeline{
     agent any
     tools{
         maven 'maven'
         jdk 'jdk'

  }
     
  stages {
       stage('Git checkout')
       {
            steps{
                
                  git branch: 'main', url: 'https://github.com/sravanin15/kanbanapp.git'
            }
       }
      stage("validate"){
          steps{
              sh 'mvn -f kanban-app -B -DskipTests clean validate'
          }
      }
       stage("compile"){
          steps{
              sh 'mvn  -f kanban-app  compile'
          }
      }
       stage("Build & SonarQube analysis") {
           
            steps {
              withSonarQubeEnv('sonarserver') {
                sh 'java -version'
                sh 'mvn -f kanban-app  sonar:sonar'
              }
            }
          }
     stage("Quality gate") {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
       
       stage("package"){
          steps{
               sh 'mvn -f kanban-app   -B -DskipTests package '
          }
      }
     
     stage('Deploy to artifactory'){
        steps{
        rtUpload(
         serverId : 'artifactory-server',
         spec :'''{
           "files" :[
           {
           "pattern":"kanban-app/target/*.jar",
           "target":"art-doc-dev-loc"
           }
           ]
         }''',
         
      )
      }
     }
      
   stage('Docker-Stage-Deployment') {
        steps{
                 docker-compose up -d 
        }
  }
            
    }
    post {  
         always {  
             echo 'This will always run'  
         }  
         success {   
           
             mail bcc: '', body: "<b>Example</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> Success!! <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: 'sravaninuthanapati15@gmail.com', mimeType: 'text/html', replyTo: '', subject: "ERROR CI: Project name -> ${env.JOB_NAME}", to: "sravaninuthanapati15@gmail.com"; 
        
         }
         failure {  
             mail bcc: '', body: "<b>Example</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: 'sravaninuthanapati15@gmail.com', mimeType: 'text/html', replyTo: '', subject: "ERROR CI: Project name -> ${env.JOB_NAME}", to: "sravaninuthanapati15@gmail.com";  
         }  
         
     }
  }
