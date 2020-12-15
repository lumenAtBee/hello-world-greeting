node('docker'){
	stage('Poll'){
		scm checkout
	} // END stage
	
	stage('Build & Unit Test'){
		sh 'mvn clean verify -DskipITs=true'
		junit '**/target/surefire-reports/TEST-*.xml'
		archive 'target/*.war'
 
	} // END stage
	
	stage('Static Code Analysis'){
		sh 'mvn clean verify sonar:sonar -Dsonar.projectName=General\ Code\ Analysis -Dsonar.projectKey=codeAnalysis -Dsonar.projectVersion=$BUILD_NUMBER'
	} // END stage

	stage('Integration Test'){
		sh 'mvn clean verify -Dsurefire.skip=true'
		junit '**/target/failsafe-reports/TEST-*.xml'
		archive 'target/*.war'
	} // END stage
	
	stage('Publish'){
	//	def server = Artifactory.server 'CI-Local-Artifactory-Server'
	//	def uploadSpec = """{
	//		"files":[
	//			{
	//				"pattern":"target/hello-*.war",
	//				"target":"helloworld-greeting-project/${BUILD_NUMBER}/",
	//				"props":"Integration-Tested=Yes;Performance-Tested=No"
	//			}
	//		]
	//	}"""
	//	server.upload(uploadSpec)
	} // END stage

	stash includes: 'target/hello-0.0.1.war,src/pt/Hellow_Worl_Test_Plan.jmx', name: 'binaries_stash'
} // END node

node('docker-performance'){
	
	stage('Start Tomcat'){
		sh 'cd /home/jenkins/tomcat/bin ; ./startup.sh'
	} // END stage
	
	stage('Deploy'){
		unstash 'binaries_stash'
		sh 'cp target/hello-0.0.1.war /home/jenkins/tomcat/webapps'
	} // END stage

	stage('Performance Testing'){
		sh 'cd /opt/jmeter/bin/ ; ./jmeter.sh -n -t $WORKSPACE/src/pt/Hellow_Worl_Test_Plan.jmx -l $WORKSPACE/test_report.jtl'
		step ([$class: 'ArtifactArchiver', artifacts: '**/*.jtl'])
	} // END stage
	
	stage('Promote build in Artifactory'){
		//withCredentials([usernameColonPassword(credentialsId: 'artifactory-account', variable: 'credentials')]){
		//	sh 'curl -u ${credentials} -X PUT 'http://localhost:8081/artifactory/api/storage/example-project/${BUILD_NUMBER}/hello-0.0.1.war?properties=Performance-Tested=Yes'
		//} // END withCredentials
	} // END stage
	
} // END node
