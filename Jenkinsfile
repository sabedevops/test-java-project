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
				sh "if ![ -d '/var/www/html/rectangles/all/${env.BRANCH_NAME}' ]; then mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}; fi"
			    sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
			}
		}
		stage ('Running on CentOS') {
			agent {
				label 'CentOS'
			}
			steps {
				sh "wget http://sabe-centos7-01/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
				sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
			}	
		}
		stage("Test on Debian"){
			agent {
				docker 'openjdk:8u121-jre'
			}
			steps {
				sh "wget http://sabe-centos7-01/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
				sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
			}
		}
		stage('Promote to Green') {
			agent {
				label 'apache'
			}
			when {
				branch 'master'
			}
			steps {
				sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
			}
		}
		stage ('Promote Development Branch to Master') {
			agent {
				label 'apache'
			}
			when {
				branch 'development'
			}
			steps {
				echo "Stashing Any Local Changes"
				sh 'git stash'
				echo "Checking Out Development"
				sh 'git checkout development'
				echo "Checking Out Master Branch"
				sh 'git checkout master'
				echo "Merging Development into Master Branch"
				sh 'git merge development'
				echo 'Pushing to Origin Master'
				sh 'git push origin master'
			}
		}		
			
    }
}
