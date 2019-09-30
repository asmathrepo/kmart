properties([
    parameters([
        [$class: 'ChoiceParameter', 
	        choiceType: 'PT_SINGLE_SELECT', 
	            description: 'Select the Env Name from the Dropdown List', 
	            filterLength: 1, 
	            filterable: true, 
	            name: 'Env', 
	            randomName: 'choice-parameter-988858984342214', 
	            script: [
	                $class: 'GroovyScript', 
		            fallbackScript: [
			            classpath: [], 
			            sandbox: false, 
			            script: 
				            'return ["error"]'
		              ], 
		              script: [
			            classpath: [], 
			            sandbox: false, 
			            		script: 
			            		    '''return[\'\',\'Dev\',\'Stage\',\'QA\',\'Prod\']'''
		              ]
	            ]
        ],

        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'Select the Server from the Dropdown List', 
            filterLength: 1, 
            filterable: true, 
            name: 'Server', 
            randomName: 'choice-parameter-990812439021766', 
            referencedParameters: 'Env', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
	            classpath: [], 
	            sandbox: false, 
	            script: 
		             'return ["Unknown Environmnet"]'
	            ],
	            script: [
		            classpath: [], 
		            sandbox: false, 
		            script: 
			        '''if (Env.equals("Prod")) {
				            return ["Prod1", "Prod2", "Prod3", "Prod4"]
				            
				        }
				        else if (Env.equals("Stage")) {
				            return ["Stage1", "Stage2"]
				        }
				        else if (Env.equals("QA")) {
				            return ["QA1", "QA2", "QA3"]
				        }
				        else if (Env.equals("Dev")) {
				            return ["Dev1"]
				        }
				        else {
                            return ["Select an Environment from Drop down"]
                        }
			          '''
			]
		]
]

])
])

pipeline {
    agent any
    
    stages {
        
        stage('SCM Checkout'){
            
        steps {
            echo "Checking out the code from SCM"
            
            checkout([$class: 'GitSCM', 
            branches: [[name: '*/master']], 
            doGenerateSubmoduleConfigurations: false, 
            extensions: [[$class: 'PerBuildTag']], 
            submoduleCfg: [], 
            userRemoteConfigs: [[credentialsId: '4ce6368a-fdff-4730-8ca7-39e6e26edcc7', 
            name: 'gitcredentials', 
            url: 'https://github.com/Ashabbe/kmart.git']]])
            
            }
            
        }
        
        stage ('Build') {
            
            steps {
                
                echo "Building source input"
                script {
			bat '"C:\\Program Files (x86)\\New folder\\MSBuild.exe" C:\\Users\\shaik.asha\\Desktop\\s\\MICROFOCUS-CICD-BUILD.sln'
		        bat("xcopy /y C:\\Users\\shaik.asha\\Desktop\\s\\BATCH\\COPYLIB\\PKZB.BASE.COPYLIB\\Copybook1.cpy C:\\Users\\shaik.asha\\Documents\\testbuild")
		}
		    }
		}
        
        stage(EnvCheck) {
            
            steps {
                
                echo "Please choose the Environmnet to proceed with the Deployment"
                
                script{
                     
                    if (params.Server.equals("Select an Environment from Drop down")) {
                        echo"Couldn't find the server"
			echo "Must be the first build after pipeline deployment. Abortign the build"
                        currentBuild.result = 'ABORTED'

                    }
                    else if (params.Env.equals("")){
                        echo "Unable to find the Environmnet,Abortign the build"
                        currentBuild.result = 'ABORTED'
                        
                    }
                    
                    else {
                    echo "Deploying to the environmnet ${params.Env}"
                    echo "Deploying to the server ${params.Server}"
                        
                    }
                }
            }
        }
        
        stage('Deploy approval'){
            when {
                    expression {
                        
                            params.Env == 'Prod'
                        }
                    }
            
            steps {
                
                echo "Entering into the approval step for deploying the output .dll files to the target Environment"
                
                script {
                def userInput = false
                userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput
                    if(userInput == true) {
                    echo "approved"
                    }
                    else {
                    echo "Action was aborted by ${userInput}"
                    }
                }
            }
        }
        
        stage(Filecopy) {
            
            when {  
                    expression {
                            params.Server == 'Prod1'
                        }
                    }
            
            steps {
                
                echo "In the process of deploying the binary  to target path"
                
                script{
                     
                    if (params.Server.equals("Prod1")){
                         bat  'scp -i C:\\Users\\shaik.asha\\Downloads\\win786.pem C:\\Users\\shaik.asha\\Desktop\\s\\MICROFOCUS-CICD-BUILD.sln ec2-user@13.233.150.43:/home/ec2-user/'
                         
                    }
                    echo "Deployed Successfully"
                }
                    
			}
        }
        
        stage("Pushing the Tag to Github with the build number") {
            
        
            
            environment { 
                GIT_TAG = "jenkins-$BUILD_NUMBER"
            }
            steps {
				withCredentials([usernamePassword(credentialsId: 'gitcredentials', passwordVariable: 'password', usernameVariable: 'username')]) {
                    bat('''                   
				   git push origin --tags
                ''')
                }
            }
        }
        
        stage('S3_Upload') {
		    steps {
                    dir('C:\\Users\\shaik.asha\\Desktop'){
                    pwd(); //Log current directory
                    withAWS(region:'us-east-1',credentials:'AWSS3') {
                        
                    // Upload files from working directory 'dist' in your project workspace
                    s3Upload(bucket:"msbuild123", workingDir:'s', includePathPattern:'**/*.sln');
                    }
                }
            }
	    }
    }
}

