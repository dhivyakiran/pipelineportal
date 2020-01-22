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
			   sh "cp -R /home/jenkins/portal/portals/aflac ."
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
   /* stage("TS Linting") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
           sh 'npm run affected:lint'
        }
    }
    stage("Unit Testcase") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            sh 'npm run affected:test'
        }
    }
    stage("Sonar Code Coverage") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            sh 'npm run code-coverage'
        }
    }*/
    stage("SonarQube code analysis") 
    {
        when {expression{(pipelinetype != "deploy")}}
          //environment { scannerHome = tool 'SonarQubeScanner' }
        steps {
           withSonarQubeEnv('sonarqube') { sh "/opt/Jenkins/sonar-scanner-4.2.0.1873/bin/sonar-scanner"}
          //sh "sonar-scanner -X"
        }
     }
     stage("SonarQube Quality Gate") 
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
    /* stage("Security scan") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            echo "security scan"
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
		    sh "cp -r aflac ${appdata.artifact[i]}"
		    sh "rm -rf aflac/apps/sales*"
		  }
                 zip archive: true, dir: "aflac", zipFile: appdata.artifact[i]+"/"+"${currentBuild.number}/"+appdata.artifact[i]+"_${currentBuild.number}.zip" 
                 nexusArtifactUploader artifacts: [[artifactId: appdata.artifact[i], file: appdata.artifact[i]+"/"+"${currentBuild.number}/"+appdata.artifact[i]+"_${currentBuild.number}.zip", type:'zip']], credentialsId: 'nexus', groupId: mydatas.nexus.groupId, nexusUrl: mydatas.nexus.nexusUrl, nexusVersion: mydatas.nexus.nexusVersion, protocol: mydatas.nexus.protocol, repository: mydatas.nexus.repository, version: mydatas.nexus.version          
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
	       //sh "mkdir members/portal"    
               def artifact = appdata.artifact.size()
               for (int i = 0; i < artifact; i++) 
               {
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
		//sh "mkdir members/portalfiles" 
		//sh "mkdir agent/portalfiles" 
               def artifact = appdata.artifact.size()
               for (int i = 0; i < artifact; i++) 
               {
		  unzip dir: "./${appdata.artifact[i]}/portalfiles/", glob: '', zipFile: "./${appdata.artifact[i]}/portal/${appdata.artifact[i]}-${mydatas.nexus.version}.zip"
               }
            } 
          }
       }*/
   }
}

