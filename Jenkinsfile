pipeline {
	// Execute pipeline on any agent
    agent none
    environment {
	MAJOR_VERSION = 1
    }
	
	options{
		// Keep 2 builds, keep 1 artifact
		buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1' ))
	}
	// Container for a sequence of 1 or more stages
    stages {
		// Unit testing stage
		stage ('Unit Tests') {
			agent {	
				label 'apache'
			}
			steps {
				sh 'ant -f test.xml -v'
				junit 'reports/result.xml'
			}
		}
	
		// Stage named "build"	
        stage ('build') {
			agent {	
				label 'apache'
			}

            steps {
			 // Execute shell build reading 'build.xml' from current directory
             sh 'ant -f build.xml -v'
            }
			// 1 or more additional steps that are run upon the completion of the Pipeline.	
			post {
				// Run on success of all steps in 'build' stage 
				success {
					// Creates a fingerprinted artifact from objects in path 
					archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
				}
			}
		}
		stage ('deploy') {
			agent {	
				label 'apache'
			}
			steps{
			    sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
			}
		}
		stage ('Running on CentOS') {
			agent {
				label 'CentOS'
			}
			steps {
				sh "wget http://sabe-centos7-01/rectangles/all/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
				sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
			}	
		}
		stage("Test on Debian"){
			agent {
				docker 'openjdk:8u121-jre'
			}
			steps {
				sh "wget http://sabe-centos7-01/rectangles/all/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
				sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
			}
		}
		stage('Promote to Green') {
			agent {
				label 'apache'
			}
			when {
				branch 'development'
			}
			steps {
				sh "cp /var/www/html/rectangles/all/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
			}
		}		
			
    }
}
