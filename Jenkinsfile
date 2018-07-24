pipeline {
	// Execute pipeline on any agent
    agent {
		label 'master'
	} 
	
	options{
		// Keep 2 builds, keep 1 artifact
		buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1' ))
	}
	// Container for a sequence of 1 or more stages
    stages {
		// Unit testing stage
		stage ('Unit Tests') {
			steps {
				sh 'ant -f test.xml -v'
				junit 'reports/result.xml'
			}
		}
	
		// Stage named "build"	
        stage ('build') {
            steps {
			 // Execute shell build reading 'build.xml' from current directory
             sh 'ant -f build.xml -v'
            }
		}
		stage ('deploy') {
			steps{
			    sh 'cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/'
			}
		}		
			
    }
	// 1 or more additional steps that are run upon the completion of the Pipeline.	
	post {
		// Run regardless of the completion status of the Pipeline or Stage runs
		always {
			// Creates a fingerprinted artifact from objects in path 
			archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
		}
	}
}
