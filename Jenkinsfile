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
			   sh "cp -R /home/satya/portal/portals/aflac ."
              }
        }
    }
    stage('Download Dependencies')
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            sh 'npm install'
        }
    }

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
                
				
				if(appdata.artifact[i] != "sales") 
				{
				//sh "mkdir salesportal"
				sh "cp -r aflac ${appdata.artifact[i]}"
				sh "rm -rf ${appdata.artifact[i]}/aflac/apps/agent*"
				sh "rm -rf ${appdata.artifact[i]}/aflac/apps/member*"
				}

                    zip archive: true, dir: appdata.artifact[i], zipFile: appdata.artifact[i]+"/"+"${currentBuild.number}/"+appdata.artifact[i]+"_${currentBuild.number}.zip" 
                  //nexusArtifactUploader artifacts: [[artifactId: appdata.artifact[i], classifier: '', file: appdata.artifact[i]+"/"+"${currentBuild.number}/"+appdata.artifact[i]+"_${currentBuild.number}.zip", type:'zip']], credentialsId: mydatas.nexus.credentialsId, groupId: mydatas.nexus.groupId, nexusUrl: mydatas.nexus.nexusUrl, nexusVersion: mydatas.nexus.nexusUrl, protocol: 'http', repository: mydatas.nexus.repository, version: mydatas.nexus.version          
               
		}
             } 
         }
      }
	}
}

