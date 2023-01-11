node{
	stage('SCM Checkout'){
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
      stage('Build Docker Imager'){
          
      		sh 'docker build -t jprakazjp/myweb:0.0.3 .'
  }
      stage('Docker Image Push'){
      		withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
      		sh "docker login -u jprakazjp -p ${dockerPassword}"
        	}
      		sh 'docker push jprakazjp/myweb:0.0.3'
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
