pipeline {
  tools {
    maven 'Maven'
  }
  agent any
  stages {
    stage ('Initialize') {
     steps {
        sh '''
                  echo "PATH = $(PATH)"
                  exho "M2_HOME = $(M2_HOME)
            '''    
       }
    }
    
    stage ('Build') {
      sh 'mvn clean package'
    }
  }
}
