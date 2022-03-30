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
    
     
    stage ('Veracode'){
      
    docker run -it --rm \
    --env VERACODE_API_KEY_ID=f71e60239ebde6119c2b7ff19174983c \
    --env VERACODE_API_KEY_SECRET=1d5314c4a95346b5d2be96ac9b0cd2cae45cb0aa844f40157b2c36dce06558ee52c9b2ab77aa3b4828a3c6adc08e88350b23c72e96fdd2ccba9ad34280e90ab1 \
    -v /host/os/path/to/myapp/:/myapp/ \
    veracode/pipeline-scan:cmd \
        -vid $VERACODE_API_KEY_ID -vkey $VERACODE_API_KEY_SECRET \
        --file /myapp/myapp.jar
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
