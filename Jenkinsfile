node 
{
    deleteDir()
	 git branch: 'development', url: 'https://github.com/dhivyakiran/pipelineportal.git'
	 mydatas = readYaml file: "pipeline.yml"
}
pipeline 
{
agent
{
   label "${mydatas.agentname}"
}
environment 
{
   envname="${params.envname}"
}
stages 
{
   stage('Initialization') 
    {
        steps 
        {
            script 
            {
                if(envname=="dev")
                {
                  pipelinetype = "build_deploy"
                }
                else if(envname=="int"||envname=="uat" || envname=="qa" || envname=="prod")
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
    stage("Checkout") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
			dir('portal')
			{
				script
				{	
					git branch: mydatas.giturl.branch, url: mydatas.giturl.path
					
						appdata = readYaml file: envname+".yml"
					
					sh "cp -R /home/jenkins/portals/* ."
				}
			}
		}
    }
    stage('Download Dependencies')
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
			dir('portal')
			{
				sh 'npm install'
				sh 'npm run affected:apps -- --base=origin/master>temp.yml'
				sh "echo 'apps:' >affected.yml"
				sh " cat temp.yml| grep ' - '|sort>>affected.yml"
			}
		}
    }
    /*stage("Linting") 
    {
		when {expression{(pipelinetype != "deploy")}}
        steps 
        {
          sh 'npm run affected:lint -- --base=origin/master'
        } 
    }
    stage("Unit Testcase") 
    {
        when {expression{(pipelinetype != "deploy") && (mydatas.test == true)}}
        steps 
        {
            sh 'npm run affected:test -- --base=origin/master'
        }
    }
    stage("Code Coverage") 
    {
        when {expression{(pipelinetype != "deploy") && (mydatas.test == true)}}
        steps 
        {
            sh 'npm run code-coverage'
        }
    }*/
    stage("Build") 
    {
		when {expression{(pipelinetype != "deploy")}}
		steps 
		{
			dir('portal')
			{
				sh 'npm run affected:build -- --base=origin/master'
			}
		}
    }
    stage("Static code analysis") 
    {
		when {expression{(pipelinetype != "deploy") && (mydatas.sonar == true)}}
		steps 
		{
			dir('portal')
			{
				script
				{
					applist = readYaml file: "affected.yml"
					def artifact = applist.apps.size()
					def prop = ""
					for (int i = 0; i < artifact; i++) 
					{
						if(applist.apps[i]=="agent")
						{
							prop = prop + "-agent"
						}
						if(applist.apps[i]=="member")
						{
							prop = prop + "-member"
						}
						if(applist.apps[i]=="sales")
						{
							prop = prop + "-sales"
						}  
					}
					withSonarQubeEnv('sonarqube') 
					{ 
						sh "cp -r ../sonar${prop}.properties sonar-project.properties" 
						sh "/opt/Jenkins/sonar-scanner-4.2.0.1873/bin/sonar-scanner"
					}
				}
			}
		}
	}
    stage("SonarQube Quality Gate") 
    {
		when {expression{(pipelinetype != "deploy") && (mydatas.sonar == true)}}
		steps 
		{
			dir('portal')
			{
				timeout(time: 30, unit: 'SECONDS') 
				{
					waitForQualityGate abortPipeline: true
				}
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
    }
    stage('Upload the artifact')
    {
        when {expression{(pipelinetype != "deploy")}} 
        steps 
        {
			dir('portal')
			{
				script
				{
					def applist = readYaml file: "affected.yml"
					def artifact = applist.apps.size()
					for (int i = 0; i < artifact; i++) 
					{
						zip archive: true, dir: "dist/apps/${applist.apps[i]}", zipFile: "dist/apps/${applist.apps[i]}.zip"
						nexusArtifactUploader artifacts: [[artifactId: applist.apps[i], file: "dist/apps/${applist.apps[i]}.zip", type:'zip']], credentialsId: 'nexus', groupId: "${currentBuild.number}", nexusUrl: mydatas.nexus.nexusUrl, nexusVersion: mydatas.nexus.nexusVersion, protocol: mydatas.nexus.protocol, repository: mydatas.nexus.repository, version: "${currentBuild.number}"
					}
				} 
			}
     }
    }
    stage('Download the artifact')
    {
      when {expression{(pipelinetype == "deploy")}} 
		  steps
      {
			 script
        {
	        def artifact = appdata.deployment_artifacts.size()
				  for (int i = 0; i < artifact; i++) 
				  {
					sh "mkdir ${appdata.deployment_artifacts[i]}"      
					sh "wget http://${mydatas.nexus.nexusUrl}/repository/${mydatas.nexus.repository}/${appdata.deploy_version}/${appdata.deployment_artifacts[i]}/latest/${appdata.deployment_artifacts[i]}-latest.zip -P ${appdata.deployment_artifacts[i]}/"
				 }
			 }
      }
    }
    stage('Unzip the application')
    {
        when {expression{(pipelinetype == "deploy")}} 
        steps 
        {
         script
         {
				  def artifact = appdata.deployment_artifacts.size()
				  for (int i = 0; i < artifact; i++) 
				  {
					 unzip dir: "${appdata.deployment_artifacts[i]}/", glob: '', zipFile: "${appdata.deployment_artifacts[i]}/${appdata.deployment_artifacts[i]}-latest.zip"
				  }
         } 
       }
    }
}   
	post 
	{
		always 
		{
			emailext attachLog: true, body: "Result: ${currentBuild.result}", recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], subject: "Pipeline: ${currentBuild.fullDisplayName}", to: "dhivya.krish15@gmail.com,cc: dhivyakrish1491@gmail.com,bcc: SatyaNarayan.Ghosh@cognizant.com"
		}
	}
}


