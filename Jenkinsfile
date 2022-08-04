pipeline {
  agent none
  environment {   
      http_proxy = 'http://127.0.0.1:3128/'
      https_proxy = 'http://127.0.0.1:3128/'
      ftp_proxy = 'http://127.0.0.1:3128/'
      socks_proxy = 'socks://127.0.0.1:3128/'     
  }		
  stages {
      stage('Build & Test') {
	   agent {
    	      dockerfile {
      	          filename 'Dockerfile'
                  label 'slave'
    	      }
  	   }
	   steps {
	      sh 'python3 -m pytest'
	   }
       }
       stage('Code Analysis & Deploy Atrifacts') {
	   agent {
      	      label 'slave'
    	   }
	   environment {
	      scannerHome = tool 'SonarQube Scanner'
	   }
	   steps {
	      withSonarQubeEnv('admin') {
	         sh '${scannerHome}/bin/sonar-scanner \
	           -D sonar.projectKey=CI-Docker'	
	      }
           }
        }
        stage('Deploy Atrifacts') {
           agent {
      	       label 'slave'
    	   }
           steps {
               rtUpload (
                   serverId: 'JFrog',
                   spec: '''{
                      "files" :[
                            {
                               "pattern": "coverage/",
                               "target": "CI-Docker"
                            }
                       ]
                   }'''
               )
            }
        }
    }
} 
