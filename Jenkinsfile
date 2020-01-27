node 
{
   git branch: 'development', url: 'https://github.com/dhivyakiran/pipelineportal.git'
   mydatas = readYaml file: "pipeline.yml"
}
pipeline 
{
agent
{
   label "master"
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
          dir('portal'){
		  script
          {
			 deleteDir()
			 
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
		dir('portal'){
        steps 
        {
            sh 'npm install'
        }
    }
	}
   /* stage("TS Linting") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
          sh 'npm run affected:lint -- --plain --base development'
        }
    }*/
    /*stage("Unit Testcase") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            sh 'npm run affected:test -- --plain --base development'
        }
    }*/
    /*stage("Code Coverage") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            sh 'npm run code-coverage'
        }
    } */
	
	/*stage("Build") 
    {
         when {expression{(pipelinetype != "deploy")}}
        steps 
        {
           sh 'npm run affected:build -- --plain --base development'
		   sh 'npm run build'
        }
    }*/
    stage("SonarQube code analysis") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
	{
	dir('portal'){
	 script
	 {
	   def artifact = appdata.artifact.size()
	   for (int i = 0; i < artifact; i++) 
        {
	     if(appdata.artifact[i]=="agent")
	     {
			sh "cp -r sonar-agent.properties sonar-project.properties" 
		 }
	     if(appdata.artifact[i]=="member")
	      {
			sh "cp -r sonar-member.properties sonar-project.properties" 
		   }
		 if(appdata.artifact[i]=="sales")
		 {
			sh "cp -r sonar-sales.properties sonar-project.properties" 
		 }  
		 withSonarQubeEnv('sonarqube') 
                { 
                   sh "/opt/Jenkins/sonar-scanner-4.2.0.1873/bin/sonar-scanner"
	        }
	     
	    }
          }
        }
     }
	 }
     /*stage("SonarQube Quality Gate") 
     {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            timeout(time: 30, unit: 'SECONDS') 
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
    }
	
	
	
   stage('Upload the artifact')
    {
        when {expression{(pipelinetype != "deploy")}} 
        steps 
        {
            script
            {
                def artifact = appdata.artifact.size()
                for (int i = 0; i < artifact; i++) 
                {
		     	zip archive: true, dir: "/dist/apps/${appdata.artifact[i]}", zipFile: appdata.artifact[i]+".zip" 
                nexusArtifactUploader artifacts: [[artifactId: appdata.artifact[i], file: "/dist/apps/"+appdata.artifact[i].zip", type:'zip']], credentialsId: 'nexus', groupId: mydatas.nexus.groupId, nexusUrl: mydatas.nexus.nexusUrl, nexusVersion: mydatas.nexus.nexusVersion, protocol: mydatas.nexus.protocol, repository: mydatas.nexus.repository, version: mydatas.nexus.version          
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
				sh "mkdir ${appdata.artifact[i]}/portal"      
                sh "wget http://${mydatas.nexus.nexusUrl}/repository/${mydatas.nexus.repository}/${mydatas.nexus.groupId}/${appdata.artifact[i]}/${mydatas.nexus.version}/${appdata.artifact[i]}-${mydatas.nexus.version}.zip -P ./${appdata.artifact[i]}/portal/"
	       }
             }
         }
      }
      stage('Unzip the application')
      {
         when {expression{(pipelinetype != "deploy")}} 
         steps 
         {
            script
            {
	      
               def artifact = appdata.artifact.size()
               for (int i = 0; i < artifact; i++) 
               {
		   sh "mkdir ${appdata.artifact[i]}/portalfiles"      
		  unzip dir: "./${appdata.artifact[i]}/portalfiles/", glob: '', zipFile: "./${appdata.artifact[i]}/portal/${appdata.artifact[i]}-${mydatas.nexus.version}.zip"
               }
            } 
          }
       }*/
   }
  /* post 
   {
     always 
     {
        emailext body: "${currentBuild.absoluteUrl} has result ${currentBuild.result}", recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], subject: "${currentBuild.result} pipeline: ${currentBuild.fullDisplayName}"
     }
   }*/
}

