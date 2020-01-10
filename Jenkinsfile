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
    /*stage('clean workspace')
    {
      steps
      {
         cleanWs()
      }
    }*/
    stage('Source code checkout') 
    {
        steps 
        {
           script 
            {
                git branch: mydatas.giturl.branch, url: mydatas.giturl.path
                appdata = readYaml file: "app.yml"
                //echo "Build url:${currentBuild.absoluteUrl}"
             }
         }
     }
    stage('Download Dependencies')
    {
        when {expression{(appdata.env == "dev") || (appdata.env == "int")}}
        steps 
        {
            nodejs(nodeJSInstallationName: 'NodeJS')
            {
            sh 'npm install'
            }
        }
    }
   stage('Zip the app')
    {
        when {expression{(appdata.env == "dev") || (appdata.env == "int")}}    
        steps 
        {
            script
            {
                def artifact = appdata.artifact.size()
                for (int i = 0; i < artifact; i++) 
                {
                    zip archive: true, dir: appdata.artifact[i], zipFile: appdata.artifact[i]+"/"+"${currentBuild.number}/"+appdata.artifact[i]+"_${currentBuild.number}.zip" 
                    nexusArtifactUploader artifacts: [[artifactId: appdata.artifact[i], classifier: '', file: appdata.artifact[i]+"/"+"${currentBuild.number}/"+appdata.artifact[i]+"_${currentBuild.number}.zip", type:'zip']], credentialsId: 'nexus', groupId: mydatas.nexus.groupId, nexusUrl: mydatas.nexus.nexusUrl, nexusVersion: mydatas.nexus.nexusUrl, protocol: 'http', repository: mydatas.nexus.repository, version: mydatas.nexus.version
               
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
      stage('UnZip the app')
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
       }
   }
}
