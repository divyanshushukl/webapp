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
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/cehkunal/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
         sh 'cp /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml .'
        
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
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@52.34.248.152:/home/ubuntu/apache-tomcat-10.0.20/webapps/webapp.war'
              }      
           }       
    }

    stage ('DAST') {
      steps {
        sshagent(['zap']) {
         sh 'docker run -t owasp/zap2docker-stable zap-baseline.py -t http://172.31.12.62:8080/webapp/ || true'
        }
      }
      
      post {
      always {
        archiveArtifacts artifacts: '**/*', onlyIfSuccessful: true
        }
    }
    
    }
 
    
}
}
