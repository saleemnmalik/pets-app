pipeline{

	agent any
	tools {
             maven 'maven3'
          }
		stages{
			stage('Maven Build/Package'){
				steps{
					sh 'mvn clean package'
				}
			}

		stage('Nexus Deploy'){
				steps{
				script{
					def pomFile = readMavenPom file: 'pom.xml'
					def version = pomFile.version
					def nexusRepo = version.endsWith("SNAPSHOT") ? "pets-app-snapshot" : "pets-app-release"
				nexusArtifactUploader artifacts: [[artifactId: 'pets-app', classifier: '', file: 'target/pets-app.war', type: 'war']],
				 credentialsId: 'nexus3',
				  groupId: 'in.javahome', 
				  nexusUrl: '172.31.37.165:8081', 
				  nexusVersion: 'nexus3', 
				  protocol: 'http', 
				  repository: nexusRepo, 
				  version: version	
					}
				}
			}

			stage('Tomcat Deploy'){
				steps{
					def userHost = "ec2-user@172.31.45.34"
					def tomcatBin = "ec2-user@172.31.45.34 /opt/tomcat8/bin"
					sshagent(['tomcat-dev']) {
						sh "scp -o StrictHostKeyChecking=no target/*.war ${userHost}:/opt/tomcat8/webapps/pets-app.war"
						sh "ssh ${tomcatBin}/shutdown.sh"
						sh "ssh ${tomcatBin}/startup.sh"

					}					
				}
			}

	}
}	

