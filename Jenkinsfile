pipeline {
	# Execute pipeline on any agent
    agent any 
	
	options{
		# Keep 2 builds, keep 1 artifact
		buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1' ))
	}
	# Container for a sequence of 1 or more stages
    stages {
		# Stage named "build"	
        stage ('build') {
            steps {
			 # Execute shell build reading 'build.xml' from current directory
             sh 'ant -f build.xml -v'
            }
        }
    }
	# 1 or more additional steps that are run upon the completion of the Pipeline.	
	post {
		# Run regardless of the completion status of the Pipeline or Stage runs
		always {
			# Creates a fingerprinted artifact from objects in path 
			archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
		}
	}
}
