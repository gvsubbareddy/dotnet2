pipeline {
  agent any
  parameters {
    choice choices: ['INT', 'mkt','PV'], description: 'Deploy to Environment', name: 'TARGET_ENVIRONMENT'
    }
  stages {
    stage('checkout') {
        steps {
            script {
                 echo "TARGET_ENVIRONMENT: ${params.TARGET_ENVIRONMENT}"
                    if("${params.TARGET_ENVIRONMENT}" == "INT") {
                        echo 'checkout the code from the develop'
                        git(branch: 'INT', credentialsId: 'GitHubCredentials', url: 'https://github.com/gvsubbareddy/dotnet2.git', changelog: true)
                    } else {
                        echo 'checkout the code from master'
                        git(branch: 'master', credentialsId: 'GitHubCredentials', url: 'https://github.com/gvsubbareddy/dotnet2.git', changelog: true)
                    }
            }
        }
    }
            
    stage('build') {
      steps {
        echo "...Building. Workspace:  ${WORKSPACE}, Build number: ${BUILD_NUMBER}"
        sh 'scl enable rh-dotnet21 bash'
        sh 'cd $WORKSPACE'
        sh '/opt/rh/rh-dotnet21/root/usr/bin/dotnet build'
        sh '/opt/rh/rh-dotnet21/root/usr/bin/dotnet publish'
        sh 'cd $WORKSPACE/bin/Debug/netcoreapp2.1/publish'
        sh 'cp -f /var/lib/jenkins/workspace/appsettings.json $WORKSPACE/bin/Debug/netcoreapp2.1/publish'
        sh 'zip -r evodashboard-${BUILD_NUMBER}.zip $WORKSPACE/bin/Debug/netcoreapp2.1/publish/'
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
                echo "TARGET_ENVIRONMENT: ${params.TARGET_ENVIRONMENT}"
                if("${params.TARGET_ENVIRONMENT}" == "INT") {
                    echo 'Deploying to INT'
                    /*withCredentials([azureServicePrincipal(credentialsId: 'MyAzureServicePrincipal',
                                        subscriptionIdVariable: 'ac3b84b7-8679-4040-8278-6adf62cdff7b',
                                        clientIdVariable: '2fad08bc-0d77-4d67-80b5-c6d37fc5c528',
                                        clientSecretVariable: '/DrXJtyn/aZcq+0DO3nBwoHFoUqoAsrg1UIGFIIVe8o=',
                                        tenantIdVariable: '47dbbae6-b4da-4a0b-9fb0-72fa25ad79dc')]) run the command: az account show
                    */
                    withCredentials([azureServicePrincipal(credentialsId: 'MyAzureServicePrincipal',
                                        subscriptionIdVariable: 'SUBSCRIPTIONIDVARIABLE',
                                        clientIdVariable: 'CLIENTIDVARIABLE',
                                        clientSecretVariable: 'CLIENTSECRETVARIABLE',
                                        tenantIdVariable: 'TENANTIDVARIABLE')]) {
                        sh 'echo subscriptionIdVariable=$SUBSCRIPTIONIDVARIABLE clientIdVariable=$CLIENTIDVARIABLE clientSecretVariable=$CLIENTSECRETVARIABLE tenantIdVariable=$TENANTIDVARIABLE'
                        sh 'az webapp stop -g myresourcegroup  -n subbuwebapp1'
                        sh 'az webapp deployment source config-zip -g myresourcegroup  -n subbuwebapp1  --src evodashboard-${BUILD_NUMBER}.zip'
                        sh 'az webapp start source config-zip -g myresourcegroup  -n subbuwebapp1'
                        /*sh 'az login --service-principal -u $CLIENTIDVARIABLE -p $CLIENTSECRETVARIABLE -t $TENANTIDVARIABLE'
                        sh 'az account set -s $SUBSCRIPTIONIDVARIABLE'
                        sh 'az resource list'*/
                    }
                } else if("${params.TARGET_ENVIRONMENT}" == "mkt") {
                    echo 'Deploying to mkt'
                    /*  sh 'az webapp stop -g myresourcegroup  -n subbuwebapp1'
                    sh 'az webapp deployment source config-zip -g myresourcegroup  -n subbuwebapp1  --src evodashboard-${BUILD_NUMBER}.zip'
                    sh 'az webapp start source config-zip -g myresourcegroup  -n subbuwebapp1'*/
                } else if("${params.TARGET_ENVIRONMENT}" == "pv") {
                    echo 'Deploying to pv'
                } else {
                    error 'deployment to ${params.TARGET_ENVIRONMENT} is not supported'
                }
                        
            }
                
        }
    }
    stage('HealthCheck') {
      steps {
        script {
            echo 'check if the webapp is up'
            def response = httpRequest 'https://google.com'
            println("Status: "+response.status)
            println("Content: "+response.content)
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

