node 
{
   git url: 'https://github.com/dhivyakiran/pipelineportal.git'
   mydatas = readYaml file: "pipeline.yml"
}
pipeline 
{
agent
{
   label "master"
}

stages 
{
   stage('Source code checkout') 
    {
       when { changeset "pipeline.yml" }
        steps 
        {
           script 
            {
                git branch: mydatas.giturl.branch, url: mydatas.giturl.path
                appdata = readYaml file: "app.yml"
                if(appdata.env=="dev" || appdata.env=="int")
                {
                  pipelinetype = "build"
                }
                else if(appdata.env=="sit" || appdata.env=="qa")
                {
                  pipelinetype = "build and deploy"
                }
                else
                {
                  pipelinetype = "deploy"
                }
               //echo "Build url:${currentBuild.absoluteUrl}}"
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
   /*stage('Zip the app')
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
      stage('Unzip the app')
      {
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
       }*/
   }
}
