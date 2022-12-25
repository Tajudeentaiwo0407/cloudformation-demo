pipeline{
    agent any
    environment{
        AWS_SECRET_ACCESS_KEY = credentials('secret_key')
        AWS_ACCESS_KEY_ID = credentials('access_id')
        BRANCH = main
    }
    parameters{
        choices(name:"Action" , choices: ['apply' 'destroy'])
        choices(name:'environment', choices: ['live','dev'])
    }

    stages{
        // checkout code
        stage("checkout code"){
            step{
                script{
                    if (${params.environment} == live){
                        sh"checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/Osiephri/Dev_che1']]])"
                    }
                    else{
                        echo "Failed to checkout"
                    }
                }
            }
        }

        //validate cloudformation template
        stage("CLOUDFORMATION VALIDATION"){
            step{
                sh 'aws cloudformation validate-template --template-body file://./iam.yml'
            }
        }

        //create cloudformation stack
        stage("CLOUDFORMATION STACK CREATION"){
            step{
                sh 'aws cloudformation create-stack --stack-name myteststack --template-body file://iam.yml --capabilities CAPABILITY_NAMED_IAM --enable-termination-protection'
            }
        }

        //Perform action against cloudformation stack
        stage("CLOUDFORMATION STACK DELETION"){
            step{
                script{
                    if($params.Action == apply){
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