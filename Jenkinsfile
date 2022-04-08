pipeline {
  tools {
    maven 'Maven'
  }
  agent any
  stages {
    stage ('Initialized') {
      steps {
        sh '''
              echo "PATH = $(PATH)"
              echo "M2_HOME" = $(M2_HOME)"
              
           '''
      }
    }
    
    stage ('Build'){
      
     sh 'mvn clean package' 
    }
  }
  
}
