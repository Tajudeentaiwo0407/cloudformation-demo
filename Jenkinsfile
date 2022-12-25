pipeline{
    agent any
    environment{
        AWS_ACCESS_KEY_ID = credentials('access_id')
        AWS_SECRET_ACCESS_KEY = credentials('secret_key')
	AWS_DEFAULT_REGION = credentials('default_region')
        BRANCH = 'main'
    }
    parameters{
        choice(name:"Action" , choices: ['apply','destroy'])
        choice(name:'environment', choices: ['live','dev'])
    }

    stages{
        checkout code
        stage("checkout code"){
            steps{
                script{
                    if (${params.environment} == live){
                    	sh "checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Tajudeentaiwo0407/cloudformation-demo']]])"
		    }
                    else{
                        echo "Failed to checkout"
                    }
                }
            }
        }

        //validate cloudformation template
        stage("CLOUDFORMATION VALIDATION"){
            steps{
                sh 'aws cloudformation validate-template --template-body file://iam.yml'
            }
        }

        //create cloudformation stack
        stage("CLOUDFORMATION STACK CREATION"){
            steps{
                sh 'aws cloudformation create-stack --stack-name myteststack --template-body file://iam.yml --capabilities CAPABILITY_NAMED_IAM --enable-termination-protection'
            }
        }

        //Perform action against cloudformation stack
        stage("CLOUDFORMATION STACK DELETION"){
            steps{
                script{
			if(${params.Action == destroy}){
                        echo "You are applying these commands"
                        sh 'aws cloudformation update-termination-protection --no-enable-termination-protection --stack-name myteststack'
			sh 'aws clouformation delete-stack --stack-name myteststack'
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
