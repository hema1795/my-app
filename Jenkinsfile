node{
   stage('SCM Checkout'){
     git 'https://github.com/hema1795/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
    stage('SonarQube Analysis') {
	   def mvnHome =  tool name: 'maven3', type: 'maven'
	   withSonarQubeEnv('sonar') { 
	   sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   
   stage('Build Docker Imager'){
   sh 'docker build -t 17hema/myweb:0.0.2 .'
   }
   
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u 17hema -p ${dockerPassword}"
    }
   sh 'docker push 17hema/myweb:0.0.2'
   }
   
      stage('Nexus Image Push'){
   withCredentials([string(credentialsId: 'nexus', variable: 'nexuspassword')]){
   sh "docker login -u admin -p ${nexuspassword} 34.203.244.69:8083"
   sh "docker tag 17hema/myweb:0.0.2 34.203.244.69:8083/devops:1.0.0"
   sh 'docker push 34.203.244.69:8083/devops:1.0.0'
   }
      }

      stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}

  stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest 17hema/myweb:0.0.2' 
   }
      }
}
