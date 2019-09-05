#!/usr/bin/env groovy
pipeline {

agent none
	
	stages {
	  	stage('Compile') {
		   
	    		steps {
				echo "Compiled Successfully!!!";
			}
		}
		stage ('Build') {
			steps {
				script {
					def msbuild =  "C:\\Windows\\Microsoft.NET\\assembly\\GAC_64\M\SBuild\\v4.0_4.0.0.0__b03f5f7f11d50a3a\\MsBuild.exe"
					bat "${msbuild} C:\\Users\\shaik.asha\\Desktop\\s\\MICROFOCUS-CICD-BUILD.sln"
          			} 
          				
					 
			}
		}
		stage('JUnit') {
			steps {
				echo "JUnit passed Successfully!!!";
				}
		}
		stage('Deploy') {
			steps {
				echo "Pass!!!";
			}
		}
	}
 
	post {
		always {
			echo 'This will always run'
		}
		success {
			echo 'This will run if succesful'
			
		}
	}	
}





