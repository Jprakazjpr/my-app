node{
	stage('Git Checkout'){
     		git 'https://github.com/Jprakazjpr/my-app'
   }
    	stage('Compile-Package'){

 		def mvnHome =  tool name: 'maven4', type: 'maven'   
      		sh "${mvnHome}/bin/mvn clean package"
	  	sh 'mv target/myweb*.war target/newapp.war'
   }
      stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven4', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	      	sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
  }
      stage('Build Docker Image'){
          
      		sh 'docker build -t jprakazjp/myweb:0.0.3 .'
  }
      stage('Docker Image Push'){
      		withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
      		sh "docker login -u jprakazjp -p ${dockerPassword}"
        	}
      		sh 'docker push jprakazjp/myweb:0.0.3'
   }

      stage('Nexus Image Push'){
   		sh "docker login -u admin -p admin 3.239.102.203:8083"
   		sh "docker tag jprakazjp/myweb:0.0.3 3.239.102.203:8083/jpapp:1.0.0"
   		sh 'docker push 3.239.102.203:8083/jpapp:1.0.0'
   }

	stage('Remove Previous Container'){
		try{
			sh 'docker rm -f myapp'
				}catch(error){
		//  do nothing if there is an exception
	    }
    }
      	stage('Docker deployment'){
      		sh 'docker run -d -p 8090:8080 --name myapp jprakazjp/myweb:0.0.3' 
   }
}
