pipeline {
  agent any
  tools { 
        maven 'Maven_3_2_5'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=gerrysereal -Dsonar.organization=gerrysereal -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=b9ef54ebdfcd70665fcd4a9b4601996bb971ee07'
			}
        } 

	   	stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }
  }
}
