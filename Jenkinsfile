pipeline {
  agent any
  parameters {
    choice choices: ['INT', 'mkt','PV'], description: 'Deploy to Environment', name: 'TARGET_ENVIRONMENT'
    }
  stages {
    stage('build') {
      steps {
        echo "...Building. Build number: ${BUILD_NUMBER}"
        sh 'scl enable rh-dotnet21 bash'
        sh 'cd $WORKSPACE'
        sh '/opt/rh/rh-dotnet21/root/usr/bin/dotnet build'
        sh '/opt/rh/rh-dotnet21/root/usr/bin/dotnet publish'
        sh 'cd $WORKSPACE/bin/Debug/netcoreapp2.1/publish'
        sh 'zip -r evodashboard-${BUILD_NUMBER}.zip ${env.WORKSPACE}/bin/Debug/netcoreapp2.1/publish/'
        echo "done zip"
      }
    }
    
    /*stage('jfrog') {
     steps {
         script {
             def server = Artifactory.server 'artifactory-1'
             def uploadSpec = readFile 'artifactory.json'
             server.upload(uploadSpec)
         }

     }
    }*/
    stage('deploy') {
      steps {
        script {
            echo 'checkout the code from $TARGET_ENVIRONMENT'
            echo "TARGET_ENVIRONMENT: ${params.TARGET_ENVIRONMENT}"
            if("${params.TARGET_ENVIRONMENT}" == "INT") {
                echo 'Deploying to INT'
                sh 'az webapp deployment source config-zip -g myresourcegroup  -n subbuwebapp1  --src dotnet2-${BUILD_NUMBER}.zip'
            } else {
                if("${params.TARGET_ENVIRONMENT}" == "mkt") {
                    echo 'Deploying to INT'
                    sh 'az webapp deployment source config-zip -g myresourcegroup  -n subbuwebapp1  --src dotnet2-${BUILD_NUMBER}.zip'
                } else {
                    if("${params.TARGET_ENVIRONMENT}" == "pv") {
                        echo 'Deploying to INT'
                        sh 'az webapp deployment source config-zip -g myresourcegroup  -n subbuwebapp1  --src dotnet2-${BUILD_NUMBER}.zip'
                    } else {
                        error 'deployment to ${params.TARGET_ENVIRONMENT} is not supported'
                    }
                    
                }
                
            }
        }
      }
      
    }
    stage('notify') {
      steps {
        echo 'completed'
      }
    }
  }
}

