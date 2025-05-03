pipeline {
  agent any
  tools { 
        maven "Maven_3.2.5" 
    }
  environment {
	SONAR_TOKEN = credentials('SONAR_TOKEN')
	AWS_ECR_URL = credentials('AWS_ECR_URL')
  }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=buggywebapps -Dsonar.organization=buggywebapps -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=$SOLAR_TOKEN'
			}
    }

	stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }

	stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("asg")
                 }
               }
            }
    }

	stage('Push') {
            steps {
                script{
		        docker.withRegistry('https://657954422063.dkr.ecr.us-west-2.amazonaws.com/asg', 'ecr:us-west-2:aws-credentials') {
                        app.push("latest") 

                    }
                }
            }
    	}
	    
  }
}
