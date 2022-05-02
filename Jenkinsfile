node{
   stage('SCM Checkout'){
     git 'https://github.com/Enfielderrajan/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
    stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv(credentialsId:'sonarr', credentialsIdName:'My SonarQube Server') { 
            sh "${mvnHome}/bin/mvn sonar:sonar"
       }
   }
   
    stage('Build Docker Imager'){
          sh 'docker build -t enfielderrajan/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
       sh "docker tag enfielderrajan/myweb:0.0.2 nambi05aws/myweb:0.0.2"
       withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]) {
       sh "docker login -u nambi05aws -p ${DockerHubPwd}"
   }
       sh 'docker push nambi05aws/myweb:0.0.2'
   }
    stage('Nexus Image Push'){
       sh "docker login -u admin -p admin123 54.91.127.105:8083"
       sh "docker tag nambi05aws/myweb:0.0.2 54.91.127.105:8083/nambi:1.0.0"
       sh 'docker push 54.91.127.105:8083/nambi:1.0.0'
   }
   
    stage('Remove Previous Container'){
	  try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//do nothing
   }
    stage('Docker deployment'){
        sh 'docker run -d -p 8090:8080 --name tomcattest nambi05aws/myweb:0.0.2' 
   }
}
}
