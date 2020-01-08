node 
{
   git url: 'https://github.com/dhivyakiran/pipelineportal.git'
   mydatas = readYaml file: "pipeline.yml"
}
pipeline 
{
agent
{
   label 'master'
}
stages 
{
    stage('Source code checkout') 
    {
        steps 
        {
            script 
            {
                git url: 'https://github.com/dhivyakiran/portal.git'
                appdata = readYaml file: "app.yml"
                //echo "Build url:${currentBuild.absoluteUrl}"
             }
         }
     }
    stage('Download Dependencies')
    {
        when {expression{(mydatas.pipeline != "Deploy")}}
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
        when {expression{(mydatas.pipeline != "Deploy")}}    
        steps 
        {
            script
            {
                def artifact = mydatas.artifact.size()
                for (int i = 0; i < artifact; i++) 
                {
                    zip archive: true, dir: mydatas.artifact[i], zipFile: mydatas.artifact[i]+"/"+"${currentBuild.number}/"+mydatas.artifact[i]+"_${currentBuild.number}.zip" 
                }
             } 
         }
      }
   }
}
