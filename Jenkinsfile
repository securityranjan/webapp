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
        sh 'rm owasp* || true'
        sh  'wget https://raw.githubusercontent.com/singhkranjan/webapp/master/owasp-dependency-check.sh'
        sh 'chmod +x owasp-dependency-check.sh'
        sh 'bash owasp-dependency-check.sh'
        sh 'cat /var/lib/jenkins/workspace/webapp-cicd-pipeline/odc-reports/dependency-check-report.xml'
      }
    } 
    
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
    
     stage('Veracode Pipeline Scan') {
      steps {
        sh 'curl -O https://downloads.veracode.com/securityscan/pipeline-scan-LATEST.zip'
        sh 'unzip pipeline-scan-LATEST.zip pipeline-scan.jar'
        sh 'java -jar pipeline-scan.jar \
          --veracode_api_id "${VERACODE_API_ID}" \
          --veracode_api_key "${VERACODE_API_SECRET}" \
          --file "build/libs/sample.jar" \
          --fail_on_severity="High" \
          --fail_on_cwe="80" \
          --baseline_file "${CI_BASELINE_PATH}" \
          --timeout "${CI_TIMEOUT}" \
          --project_name "${env.JOB_NAME}" \
          --project_url "${env.GIT_URL}" \
          --project_ref "${env.GIT_COMMIT}"'
      }
    }
    
    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@15.206.123.214:/prod/apache-tomcat-9.0.60/webapps/webapp.war'
              }      
           }       
    }
    
   
    
  }
}
