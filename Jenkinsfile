pipeline {
  agent any
  tools { 
        maven 'Maven_3_8_4'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=new-org-oso -Dsonar.organization=new-org-oso -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=7532fdda3111ca2329252b6853d5ce66b276f033'
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
                 app =  docker.build("oso")
                 }
               }
            }
    }

	stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://275254035816.dkr.ecr.us-west-1.amazonaws.com', 'ecr:us-west-1:aws-credentials') {
                    app.push("latest")
                    }
                }
            }
    	}

	stage('Kubernetes Deployment of ASG Bugg Web Application') {
	   steps {
	      withKubeConfig([credentialsId: 'kubelogin']) {
		  sh '/home/ec2-user/bin/kubectl delete all --all -n devsecops'
		  // sh('kubectl delete all --all -n devsecops')
		  sh ('kubectl apply -f deployment.yaml --namespace=devsecops')
		}
	      }

}
}
}
// #TOKEN ID 559b88b0-33c8-47e8-8fb8-a765dc24e5d9
