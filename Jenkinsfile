pipeline {
  agent any 
  tools {
    maven 'maven'
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
        sh 'docker run gesellix/trufflehog --json https://github.com/divyanshushukl/webapp.git > trufflehog'
        sh 'cat trufflehog'
      }
    }
    
    stage ('Source Composition Analysis') {
      steps {
         sh 'rm dependency-* || true'
         sh 'wget "https://github.com/jeremylong/DependencyCheck/releases/download/v7.0.4/dependency-check-7.0.4-release.zip" '
         sh 'unzip dependency-check-7.0.4-release.zip'
         sh 'chmod +x dependency-check/bin/dependency-check.sh'
         sh 'bash dependency-check/bin/dependency-check.sh -s .'
         sh 'cat dependency-check-report.html'
        
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
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@54.70.151.144:/tmp/apache-tomcat-10.0.20/webapps/webapp.war'
              }      
           }       
    }
    
}
}
