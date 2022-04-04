pipeline {
  agent any
    
  tools {nodejs "nodejs"}
  
  environment {
        BUILD_DATE = sh(returnStdout: true, script: "date -u +'%d-%m-%Y-%H-%M-%S'").trim()
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "https"
        NEXUS_URL = "sonatype.docker.test.coherentprojects.net"
        NEXUS_REPOSITORY = "multibranch_frontend"
        NEXUS_CREDENTIAL_ID = "nexus-user-credentials"
    }

    stages {


  stage('SonarQube analysis') {
    environment {
      SCANNER_HOME = tool 'Sonar-scanner'
    } 
   steps {
       script {
      
           withSonarQubeEnv("SonarQube") {
           sh "$SCANNER_HOME/bin/sonar-scanner  \
           -Dsonar.projectKey=multibranch_frontend \
           -Dsonar.sources=. \
           -Dsonar.css.node=. \
           -Dsonar.host.url=http://192.168.3.159:9000 \
           -Dsonar.login=4a9c38808c74a9fc1efc7fa852f86429141bb0cc"
               }
           }
       }
   }

   	
   
   stage('Build') {
      steps {
        sh 'npm install'
        sh 'npm run build'
	 }
    }
	
		 stage("Publish to Nexus Repository Manager") {
            steps {
                sh "zip -r build-${BUILD_ID}.zip build/*"
				script {
			      dir('.') {
                    def artifact_name = "build-${BUILD_ID}"
                    nexusArtifactUploader (
                        nexusVersion: NEXUS_VERSION,
                        protocol: NEXUS_PROTOCOL,
                        nexusUrl: NEXUS_URL,
                        repository: NEXUS_REPOSITORY,
                        version: "$BUILD_DATE",
                        credentialsId: NEXUS_CREDENTIAL_ID,
                        groupId: 'devops-training',
                        

						 artifacts: [
                                [artifactId: 'build', file: "${artifact_name}.zip", type: 'zip']
							]
                    )

				  }
				}
			}
		}
	}

}