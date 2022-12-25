pipeline{
    agent any
    environment{
        AWS_ACCESS_KEY_ID = credentials('access_id')
        AWS_SECRET_ACCESS_KEY = credentials('secret_key')
	AWS_DEFAULT_REGION = credentials('default_region')
        BRANCH = 'main'
    }
    parameters{
        choice(name:"Action" , choices: ['create','delete'])
        choice(name:'environment', choices: ['live','dev'])
    }

    stages{
        checkout code
        stage("checkout code"){
            steps{
                script{
                    if ( params.environment == 'live' ){
                    		checkout([$class: 'GitSCM', 
				      branches: [[name: '*/main']], 
				      extensions: [], 
				      userRemoteConfigs: [[
					      url: 'https://github.com/Tajudeentaiwo0407/cloudformation-demo']]
				])
		    }
                    else{
                        echo "Failed to checkout"
                    }
                }
            }
        }

        //Validate cloudformation template
        stage("CLOUDFORMATION VALIDATION"){
            steps{
                sh 'aws cloudformation validate-template --template-body file://iam.yml'
            }
        }

        //Create cloudformation stack
        stage("CLOUDFORMATION STACK CREATION"){
        	steps{
			script{
		    		if( params.Action == 'create' ){
                        	echo "You are creating a new stack"
			    	sh 'aws cloudformation create-stack --stack-name myteststack --template-body file://iam.yml --capabilities CAPABILITY_NAMED_IAM --enable-termination-protection'
                    		}
//                      		else{
//                          		echo "The error has been generated and mailed to you."
//                     		}
		    	}	
	    	}
        }

        //Perform action against cloudformation stack
        stage("CLOUDFORMATION STACK DELETION"){
            steps{
                script{
			if( params.Action == 'delete' ){
                        	echo "You are applying the stack"
                        	sh 'aws cloudformation update-termination-protection --no-enable-termination-protection --stack-name myteststack'
				sh 'aws cloudformation delete-stack --stack-name myteststack'
                    }
                        else{
                        	echo "The error has been generated and mailed to you."
                    }
                }
            }
        }
    }

    post{
        success{
            echo "This build succeeded"
        }
    }
}
