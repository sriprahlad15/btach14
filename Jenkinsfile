pipeline {
	agent none
	stages {
		stage ('build'){
			agent {
				label "slave"
            }
			steps {
				git 'https://github.com/venkat09docs/btach14.git'
				withEnv(["PATH+MAVEN=${tool 'maven3'}/bin"]) {
					sh "mvn clean package"		  
				}
				stash excludes: 'target/', includes: '**', name: 'source'
			}
		}
		stage ('test') {
			agent {
				label "slave"
            }
			steps {
				parallel (
					'integration': { 
						unstash 'source'
						withEnv(["PATH+MAVEN=${tool 'maven3'}/bin"]) {
							sh "mvn clean package"		  
						}		  						
					}, 'quality': {
						unstash 'source'
						withEnv(["PATH+MAVEN=${tool 'maven3'}/bin"]) {
							sh "mvn clean package"		  
						}		  						
					}
				)
			}
		}
		stage ('approve') {
			agent {
				label "slave"
            }
			steps {
				timeout(time: 7, unit: 'DAYS') {
					input message: 'Do you want to deploy?', submitter: 'admin'
				}
			}
		}
		stage ('deploy') {
			agent {
				label "slave"
            }
			steps {
				unstash 'source'
				withEnv(["PATH+MAVEN=${tool 'maven3'}/bin"]) {
					sh "mvn clean package"		  
				}				
			}
			post {
				always {
					archiveArtifacts '**/*.war'
				}
			}
		}
	}
}
