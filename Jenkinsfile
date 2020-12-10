node('docker'){
	stage('Poll'){
		scm checkout
	} // END stage
	
	stage('Build & Unit Test'){
		sh 'mvn clean verify -DskipITs=true'
		junit '**/target/surefire-reports/TEST-*.xml'
		archive 'target/*.jar'
 
	} // END stage
	
	stage('Static Code Analysis'){
		sh 'mvn clean verify sonar:sonar -Dsonar.projectName=General\ Code\ Analysis -Dsonar.projectKey=codeAnalysis -Dsonar.projectVersion=$BUILD_NUMBER'
	} // END stage

	stage('Integration Test'){
		sh 'mvn clean verify -Dsurefire.skip=true'
		junit '**/target/failsafe-reports/TEST-*.xml'
		archive 'target/*.jar'
	} // END stage
	
	stage('Publish'){
		def server = Artifactory.server 'CI-Local-Artifactory-Server'
		def uploadSpec = """{
			"files":[
				{
					"pattern":"target/hello-*.war",
					"target":"helloworld-greeting-project/${BUILD_NUMBER}/",
					"props":"Integration-Tested=Yes;Performance-Tested=No"
				}
			]
		}"""
		server.upload(uploadSpec)
	} // END stage

} // END node
