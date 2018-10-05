pipeline {
  agent any
  parameters {
    choice choices: ['INT', 'mkt','PV'], description: 'Deploy to Environment', name: 'BRANCH_NAME'
    }
  stages {
    stage('checkout') {
      steps {
        script {
            echo 'checkout the code from $BRANCH_NAME'
            echo "BRANCH_NAME: ${params.BRANCH_NAME}"
            if("${params.BRANCH_NAME}" == "INT") {
                echo 'checkout the code'
                /*git(url: 'https://github.com/gvsubbareddy/dotnet2.git', changelog: true)*/
		git(branch: ${params.BRANCH_NAME}, url: 'https://github.com/gvsubbareddy/dotnet2.git', changelog: true)
            } else {
                echo 'deployment to ${params.BRANCH_NAME} is not supported'
            }
        }
      }
    }
    stage('compile') {
      steps {
        echo "...Building. Build number: ${BUILD_NUMBER}"
        sh 'scl enable rh-dotnet21 bash'
        sh 'cd /var/lib/jenkins/workspace/pipelinetest03Oct'
        sh '/opt/rh/rh-dotnet21/root/usr/bin/dotnet build'
        sh '/opt/rh/rh-dotnet21/root/usr/bin/dotnet publish'
        sh 'cd /var/lib/jenkins/workspace/pipelinetest03Oct/bin/Debug/netcoreapp2.1/publish'
        sh 'zip -r dotnet2-${BUILD_NUMBER}.zip /var/lib/jenkins/workspace/pipelinetest03Oct/bin/Debug/netcoreapp2.1/publish/'
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
        echo 'deploying webapp to azure..'
        sh 'az webapp deployment source config-zip -g myresourcegroup  -n subbuwebapp1  --src dotnet2-${BUILD_NUMBER}.zip'
      }
    }
    stage('notify') {
      steps {
        echo 'completed'
      }
    }
  }
}

