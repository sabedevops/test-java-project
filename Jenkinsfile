pipeline {
    agent any
    stages {
        stage ('build') {
            steps {
             sh 'ant -f build.xml -v'
            }
        }
    }	
	post {
		always {
			archiveArtifactsartifacts: 'dist/*.jar', fingerprint: true
		}
	}
}
