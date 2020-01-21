node 
{
   git url: 'https://github.com/dhivyakiran/pipelineportal.git'
   mydatas = readYaml file: "pipeline.yml"
  
}

pipeline 
{
agent
{
   label "slave1"
}
environment 
{
   envname="${params.envname}"

}
stages 
{
   stage('Environment Initialization') 
    {
        steps 
        {
            
           script 
            {
                if(envname=="dev" || envname=="int")
                {
                  pipelinetype = "build_deploy"
                }
                else if(envname=="uat" || envname=="qa" || envname=="prod")
                {
                  pipelinetype = "deploy"
                }
                else
                {
                  pipelinetype = "build"
                }
               //echo "Build url:${currentBuild.absoluteUrl}}"
             }
         }
     }
    stage("Source code checkout") 
    {
       
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            script
              {
               git branch: mydatas.giturl.branch, url: mydatas.giturl.path
               appdata = readYaml file: envname+".yml"
              }
        }
    }
    stage('Download Dependencies')
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            nodejs(nodeJSInstallationName: 'NodeJS')
            {
            sh 'npm install'
            }
        }
    }
    /*stage("TS Linting") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            nodejs(nodeJSInstallationName: 'NodeJS')
            {
            sh 'npm run affected:lint'
            }
        }
    }
    stage("Unit Testcase") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            nodejs(nodeJSInstallationName: 'NodeJS')
            {
            sh 'npm run affected:test'
            }
        }
    }
    stage("Sonar Code Coverage") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            nodejs(nodeJSInstallationName: 'NodeJS')
            {
            sh 'npm run code-coverage'
            }
        }
    }
    stage("SonarQube code analysis") 
    {
        when {expression{(pipelinetype != "deploy")}}
          //environment { scannerHome = tool 'SonarQubeScanner' }
        steps {
           //withSonarQubeEnv('sonarqube') { bat "${scannerHome}/bin/sonar-scanner"}
          bat "sonar-scanner -X"
        }
     }
     stage("SonarQube Quality Gate") 
     {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            timeout(time: 10, unit: 'MINUTES') 
            {
               waitForQualityGate abortPipeline: true
            }
        }
     }
     stage("Security scan") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            echo "security scan"
        }
    }*/
   stage('zip the app and upload the artifact')
    {
        when {expression{(pipelinetype != "deploy")}} 
        steps 
        {
            script
            {
                def artifact = appdata.artifact.size()
                for (int i = 0; i < artifact; i++) 
                {
                    zip archive: true, dir: appdata.artifact[i], zipFile: appdata.artifact[i]+"/"+"${currentBuild.number}/"+appdata.artifact[i]+"_${currentBuild.number}.zip" 
                    nexusArtifactUploader artifacts: [[artifactId: appdata.artifact[i], classifier: '', file: appdata.artifact[i]+"/"+"${currentBuild.number}/"+appdata.artifact[i]+"_${currentBuild.number}.zip", type:'zip']], credentialsId: mydatas.nexus.credentialsId, groupId: mydatas.nexus.groupId, nexusUrl: mydatas.nexus.nexusUrl, nexusVersion: mydatas.nexus.nexusUrl, protocol: 'http', repository: mydatas.nexus.repository, version: mydatas.nexus.version
               
                }
             } 
         }
      }
     stage('Download the artifact')
      {
         when {expression{(pipelinetype != "build")}} 
         steps
         {
            script
            {
               def artifact = appdata.artifact.size()
               for (int i = 0; i < artifact; i++) 
               {
                  sh 'wget mydatas.nexus.protocol://mydatas.nexus.nexusUrl/repository/mydatas.nexus.repository/mydatas.nexus.groupId/appdata.artifact[i]/mydatas.nexus.version/appdata.artifact[i]-mydatas.nexus.version.zip -P ./appdata.artifact[i]/'
               }
             }
         }
      }
      stage('UnZip the application')
      {
         when {expression{(pipelinetype != "build")}} 
         steps 
         {
            script
            {
               def artifact = appdata.artifact.size()
               for (int i = 0; i < artifact; i++) 
               {
                  unzip dir: './'+appdata.artifact[i]+'/', glob: '', zipFile: appdata.artifact[i]+"-"+mydatas.nexus.version+".zip"
               }
            } 
          }
       }
       /*stage("Deploy into S3 bucket") 
       {
        when {expression{(pipelinetype != "build")}} 
        steps 
        {
           aws s3 cp $WORKSPACE/ s3://your/bucket --recursive --include "*"
        }
     }*/
   }
   post 
   {
      always 
      {
         emailext attachLog: true, body: "${currentBuild.result}: ${currentBuild.absoluteUrl}", compressLog: true, replyTo: 'dhivya.krish15@gmail.com', subject: "${currentBuild.result} pipeline: ${currentBuild.fullDisplayName}", to: 'dhivyakrish1491@gmail.com'
      }
   }
}
