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
properties([
  parameters([
    string(name: 'env', defaultValue: 'dev'),
    string(name: 'env', defaultValue: 'qa'),
    string(name: 'env', defaultValue: 'int')
  ])
])
 
/*environment 
{
   env=${params.env}
   echo "${params.env}"
} */
stages 
{
   stage('Environment Initialization') 
    {
        steps 
        {
           script 
            {
               def env = ${params.env}
                if(env=="dev" || env=="int")
                {
                  pipelinetype = "build_deploy"
                  echo "inside dev"
                }
                else if(env=="uat" || env=="qa" || env=="prod")
                {
                  pipelinetype = "deploy"
                }
                else
                {
                  pipelinetype = "build"
                   echo "inside else"
                }
               //echo "Build url:${currentBuild.absoluteUrl}}"
               echo "inside script"
               echo "${params.env}"
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
               appdata = readYaml file: "$env.yml"  
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
    stage("TS Linting") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            echo "Linting"
        }
    }
    stage("Unit Testcase") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            echo "Execute unit tests"
        }
    }
    /*stage("SonarQube code analysis") 
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
     }*/
     stage("Security scan") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            echo "security scan"
        }
    }
   /*stage('zip the app and upload the artifact')
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
       stage("Deploy into S3 bucket") 
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
         emailext body: "${currentBuild.absoluteUrl} has result ${currentBuild.result}", recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], subject: "${currentBuild.result} pipeline: ${currentBuild.fullDisplayName}"
      }
   }
}
