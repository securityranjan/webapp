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
    
  stage ('Check-Git-Secret') {
      steps {
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/singhkranjan/webapp.git > trufflehog'
        sh 'cat trufflehog'
      }
    } 
  
    
  stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
    
    stage('DAST/Qualys'){
        steps{
            getImageVulnsFromQualys imageIds: 'e0421a24221d', useGlobalConfig: true
        }
    }
    
    
    }
}
