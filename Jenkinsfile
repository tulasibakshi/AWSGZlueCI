pipeline {
    agent any

    stages {
        stage('Source') {
            steps {
              checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/tulasibakshi/AWSGZlueCI.git']]])
            }

        }
        stage('Deploy') {
            steps {
                withAWS(region: 'us-west-2')
                    {
                        cfnUpdate(stack:'gluedemocicdtest',
                        file:'gluedatalake.yaml',
                        timeoutInMinutes:10,
                        tags:['TagName=Value'],
                        pollInterval:1000)
                    }    
            }
        }
        stage('AutomatedLiveTest') {
            steps {
                withAWS(region: 'us-west-2')
                    {
                //sh 'python datalakelive_tst.py  gluedemocicdtest'
                echo "test stage"
                    }
            }    
        }
        stage('LiveTestApproval'){
            steps {
                input "Deploy to prod?"
               /** script {
                def inp = false
                inp = input "Deploy to prod?"
                echo 'userInput: ' + inp
                if(inp == true) {
                continue
            } else {
                
                echo "Action was aborted."
                break
            }
                }**/
            }    
        }
        
        stage('LiveTestCleanup'){
            steps {
                
                withAWS(region: 'us-west-2'){
                    cfnDelete(stack:'gluedemocicdtest', pollInterval:1000)
                }
            }
            }
            
            
            
    }
}
