pipeline {
  agent any 
  tools {
    maven 'Maven'
  }
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }
    
   stage ('Check-Git-Secrets') {
      steps {
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/singhkranjan/webapp.git > trufflehog'
        sh 'cat trufflehog'
      }
    }
    
  stage ('Source Composition Analysis') {
      steps {
        
        sh 'bash owasp-dependency-check.sh'
        sh 'cat /var/lib/jenkins/workspace/Webapp-DevSecOps/odc-reports/dependency-check-report.xml'
      }
    } 
 
    stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar'
          sh 'cat target/sonar/report-task.txt'
        }
      }
    }    
    
    
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
    
       
    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@3.109.4.142:/prod/apache-tomcat-9.0.60/webapps/webapp.war'
              }      
           }       
        }
    
   
    
  }
}
