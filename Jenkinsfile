pipeline {
    agent any

    stages {
        stage('Source') {
            steps {
              checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/tulasibakshi/AWSGZlueCI.git']]])
            }

        }
        stage('ValidateTemplate') {
            steps {
                script{
                    withAWS(region: 'ap-southeast-1')
                    {
                        def response = cfnValidate(file:'gluedatalake.yaml')
                        echo "template description: ${response}"
                    }  
                }    
            }
        }
        stage('TestDeploy') {
            steps {
                withAWS(region: 'ap-southeast-1',credentials:'aws-credentials')
                    {
                        cfnUpdate(stack:'gluedemocicdtest',
                        file:'gluedatalake.yaml',
                        timeoutInMinutes:10,
                        tags:['TagName=gluejob'])
                    }    
            }
        }
        stage('AutomatedLiveTest') {
            steps {
                withAWS(region: 'ap-southeast-1',credentials:'aws-credentials')
                    {
                sh 'python datalakelive_tst.py  gluedemocicdtest'
                echo "test stage"
                    }
            }    
        }
        stage('TestCleanupApproval'){
            steps {
                input "Cleanup the test environment?"
               
            }    
        }
        
        stage('LiveTestCleanup'){
            steps {
                
                withAWS(region: 'ap-southeast-1',credentials:'aws-credentials'){
                    cfnDelete(stack:'gluedemocicdtest', pollInterval:1000)
                }
            }
            }
      
    }
}
