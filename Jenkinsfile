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
    
     
     stage('Maven Build') {
        steps {
         sh 'mvn clean verify'
        }
        }
        stage('Veracode Pipeline Scan') {
        steps {
         sh 'curl -O https://downloads.veracode.com/securityscan/pipeline-scan-LATEST.zip'
         sh 'unzip pipeline-scan-LATEST.zip pipeline-scan.jar'
         sh 'java -jar pipeline-scan.jar' \
         --veracode_api_id "${VERACODE_API_ID}" \
         --veracode_api_key "${VERACODE_API_SECRET}" \
         --file "build/libs/sample.jar" \
         --fail_on_severity="Very High" \
         --fail_on_cwe="80" \
         --baseline_file "${CI_BASELINE_PATH}" \
         --timeout "${CI_TIMEOUT}" \
         --project_name "${env.JOB_NAME}" \
         --project_url "${env.GIT_URL}" \
         --project_ref "${env.GIT_COMMIT}" \
        }
        }
        }
        post {
        always {
        archiveArtifacts artifacts: 'results.json', fingerprint: true
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
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@52.66.195.83:/prod/apache-tomcat-9.0.60/webapps/webapp.war'
              }      
           }       
    }
    
   
    
  }
}
