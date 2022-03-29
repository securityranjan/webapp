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
    
    
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
    
     stage('Veracode Pipeline Scan') {
      steps {
         sh 'java -jar pipeline-scan.jar \
          --veracode_api_id "8dd5f5141e52f8c4d9b84c2479c76557" \
          --veracode_api_key "3d843641ebe458a5a0aafc858d5be6f9fb73edb846bb4a009500f9d540db3793f81a185cbe2aaca5374f84b50dfe7a2f1346465d396024ecc4b7e0c77d3ba6a0" \
          --file "target/*.war" \
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
