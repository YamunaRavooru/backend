pipeline {
    agent {label 'AGENT-1'} 
    environment{
        project='expense'
        component='backend'
        appVersion=''
        ACC_ID='982534364647'
    }
    options{
        disableConcurrentBuilds()
        timeout(time:30,unit:'MINUTES') 
    }
  
    stages{
        stage("Read Version"){
            steps{
                script{
                    def  packagesJson = readJSON file :'package.json'
                    appVersion= packagesJson.version
                    echo "version is: $appVersion"
                    }
                }
            }
     stage('Install Dependencies') {
            steps {
               script{ 
                 sh """
                    npm install
                 """
               }
            }
        }
         stage('Docker Build') {
            steps {
               script{
                withAWS(region: 'us-east-1', credentials: 'aws-creds') {
                    sh """
                    aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com

                    docker build -t  ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${project}/${component}:${appVersion} .

                    docker  push ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${project}/${component}:${appVersion}
                    """
                }
                 
               }
            }
        }

}
    post { 
    always { 
        echo 'I will always say Hello again!'
        deleteDir()
        }
    failure { 
        echo 'I will run when pipeline is failed'
        }
    success { 
        echo 'I will run when pipeline is success'
            }
    }
}