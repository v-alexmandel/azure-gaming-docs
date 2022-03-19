---
title: Azure Cloud Build Pipelines - The Azure DevOps Pipeline
description: This section of the guide shows how to set up the Azure DevOps pipeline. This is part 7 of an 8 part series.
author: Tze Lin Ong
keywords: 
ms.topic: reference-architectures
ms.date: 3/18/2022
ms.author: tzong
ms.prod: azure-gaming
---
# Section 6: The Azure DevOps Pipeline

[![Azure Cloud Build Section 6 Overview](media/cloud-build-pipeline/acb6-azdopipeline/acb6-roadmap.png)](media/cloud-build-pipeline/acb6-azdopipeline/acb6-roadmap.png)

Now we have all the pieces to finally create the pipeline in Azure DevOps. We can do this from a browser on any workstation.
- Create the pipeline
- Set the pipeline variables
- Set up Perforce tasks
- Archive files
- Copy files to Storage blob

Create the pipeline

1.	In a browser, navigate to https://dev.azure.com/<*{your Organization name}*

2.	Go to your Azure DevOps project. In the left menu blade, click on Pipelines, under Pipelines:

[![Pipelines Menu](media/cloud-build-pipeline/acb6-azdopipeline/pipelinemenu.png)](media/cloud-build-pipeline/acb6-azdopipeline/pipelinemenu.png)

3.	On the Create your first Pipeline page, click **Create pipeline**.

4.	For where is your code? Choose Azure Repos Git and pick the Repo you just created in the previous step.

[![Pipeline setup 1](media/cloud-build-pipeline/acb6-azdopipeline/pipelinesetup1.png)](media/cloud-build-pipeline/acb6-azdopipeline/pipelinesetup1.png)


5.	 Select Existing Azure Pipelines YAML file. Pick the Starter pipeline.

6.	You will advance to the “Review your pipeline YAML” page.

[![Pipeline setup 2](media/cloud-build-pipeline/acb6-azdopipeline/pipelinesetup2.png)](media/cloud-build-pipeline/acb6-azdopipeline/pipelinesetup2.png)

7.	We will overwrite almost all the boilerplate code in the following steps. But for now, click on the down chevron next to Save and run, select Save. enter a commit message, and hit “Save” again.

8.	Once the pipeline saves, click Edit.

[![Pipeline setup 3](media/cloud-build-pipeline/acb6-azdopipeline/pipelinesetup3.png)](media/cloud-build-pipeline/acb6-azdopipeline/pipelinesetup3.png)


Now let’s modify the existing pipeline. Edit the YAML script as follows:

9.	Delete all the boilerplate YAML script and insert the following:

```yaml

trigger: none

pool: 
  name: Default

variables:
  #--- insert your own values in these variables ---
  p4depotpath: <insert your own values>
  p4port: <insert your own values>
  fingerprint: <insert your own values>
  p4user: <insert your own values>
  p4client: <insert your own values>
  p4root: <insert your own values>
  serviceconnection: <insert your own values>
  storagename: <insert your own values>
  containername: <insert your own values>
  
  #--- do not change the values below ---
  ChangelistNumber: 0
  gametitle: ShooterGame
  buildtag: 
  outputfolder: c:\BuildOutput

steps:
- task: FindPerforceChangelistNumber@1
     displayName: Find Perforce CL Number
  inputs:
    STATUS: 'submitted'
    DEPOTPATH: '$(p4depotpath)'
    P4PORT: '$(p4port)'
    ssl: true
    fingerprint: '$(fingerprint)'
    P4USER: '$(p4user)'
    P4PASSWD: '$(p4pass)'
    
- task: Perforce@1
  displayName: Sync Perforce to CL
  inputs:
    UseDepotPath: true
    DepotPath: '$(p4depotpath)'
    P4ROOT: '$(p4root)'
    CreateWorkspace: false
    Sync: true
    Clean: false
    Force: false
    synctype: 'changelist'
    CHANGELIST: '$(ChangelistNumber)'
    P4CLIENT: '$(p4client)'
    P4PORT: '$(p4port)'
    ssl: true
    fingerprint: '$(fingerprint)'
    P4USER: '$(p4user)'
    P4PASSWD: '$(p4pass)'
    
- task: PowerShell@2
     displayName: Build the game
  inputs:
    targetType: 'inline'
    script: |

      Write-Host "Building game"
      cd "C:\Epic Games\UE_4.27\Engine\Build\BatchFiles"
      .\RunUAT.bat BuildCookRun -project="$(p4root)\$(gametitle)\$(gametitle).uproject" -noP4 -platform=Win64 -clientconfig=Development -serverconfig=Development -cook -allmaps -build -stage -pak -archive -archivedirectory="$(outputfolder)"
     
      Write-Host "Setting build tag"
      $d = Get-Date
      $tag = '$(gametitle)' + "-" + $d.year.tostring() + $d.month.tostring() + $d.day.tostring() + $d.hour.tostring() + $d.minute.tostring() + $d.second.tostring() + "-CL" + $(ChangelistNumber)
      Write-Output ("##vso[task.setvariable variable=buildtag]$tag")

- task: ArchiveFiles@2
  inputs:
    rootFolderOrFile: '$(outputfolder)\WindowsNoEditor'
    includeRootFolder: false
    archiveType: '7z'
    sevenZipCompression: 'fastest'
    archiveFile: '$(Build.ArtifactStagingDirectory)/$(buildtag).$(Build.BuildId).zip'
    replaceExistingArchive: true
    verbose: true

- task: AzureFileCopy@4
     displayName: Copy build to storage account
  inputs:
    SourcePath: '$(Build.ArtifactStagingDirectory)/$(buildtag).$(Build.BuildId).zip'
    azureSubscription: '$(serviceconnection)'
    Destination: 'AzureBlob'
    storage: '$(storagename)'
    ContainerName: '$(containername)'
```
Populate the variables with your values

- p4depotpath: the depot path of the build user, e.g. //depot/...
- p4port: the connection string of your perforce server, e.g. ssl:*{ip address}*:1666
- fingerprint: the SSL fingerprint of your perforce server, e.g. BA:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:F4
- p4user: the name of your p4 build user, e.g. build_user
- p4client: the client workspace name of your build user, e.g. demobuild1-ws
- p4root: the perforce root on your build machine, e.g. c:\p4depots\depot
- serviceconnection: the name of the service connection you created in step LINK
- storagename: the name of the Azure Storage Account you created in step LINK
- containername: the name of the Container you created in step LINK

Once you have filled in your values for each variable, your script should look something like this:

[![Pipeline setup 4](media/cloud-build-pipeline/acb6-azdopipeline/pipelinesetup4.png)](media/cloud-build-pipeline/acb6-azdopipeline/pipelinesetup4.png)


## Set a secret pipeline variable

There’s one variable, the perforce user password, that we want to keep hidden. To do this:

10. Click on Variables in the upper-right of the screen

[![Secret variable 1](media/cloud-build-pipeline/acb6-azdopipeline/variables1.png)](media/cloud-build-pipeline/acb6-azdopipeline/variables1.png)

11.	Click on New variable (for the first one) 
12.	Insert the following data

- Name: p4pass
- Value: *{your p4 build user password}*
- Keep this value secret: check this box

13.	Click Save. Your variables blade should look something like the following.

[![Secret variable 2](media/cloud-build-pipeline/acb6-azdopipeline/variables2.png)](media/cloud-build-pipeline/acb6-azdopipeline/variables2.png)


## Pipeline explanation

There are basically 8 sections to the pipeline.

- Trigger. This tells the pipeline not to trigger on changes to the Azure DevOps repo, because we are going to install our own trigger later.
- Pool. This requests a build agent from the Default agent pool. There is only one build agent in there, the one you set up in Section 4.
- Variables. This section defines all the variables the pipeline will consume downstream.
- Task: Find Perforce Change List number. Finds the latest change list number at head and stores it to variable ChangelistNumber.
- Task: Perforce. Syncs the depot on the build machine to the ChangelistNumber. 
- Task: Powershell script.
    - Executes an inline powershell script that calls the Unreal Automation Tool to build the game at a specific location in the depot
    - Stores the resulting build to the output folder
    - Renames the file with a build tag comprised of date/time and ChangelistNumber
- Task: Archive File. Zips up the loose files in the build output into a single compressed file.
- Task: Azure File Copy. Copies the file into an Azure Blob store for downstream use.

## Test Run without Perforce trigger
At this point, you can test the pipeline directly in Azure DevOps to see if it works.

14. In the top-right corner of the webpage, click Run.

[![Test run](media/cloud-build-pipeline/acb6-azdopipeline/variables1.png)](media/cloud-build-pipeline/acb6-azdopipeline/variables1.png)

Since this is the first time the pipeline is run, you will have to grant permissions for the pipeline to access resources:

[![Pipeline first run](media/cloud-build-pipeline/acb6-azdopipeline/pipelinefirstrun.png)](media/cloud-build-pipeline/acb6-azdopipeline/pipelinefirstrun.png)


15.  Click **Permit**. You can track the progress of the build by following the pipeline output. 

## Install the Helix Core commit server trigger

This is the final setup step! 
We are ready to link Perforce with the Azure DevOps pipeline with a Perforce trigger.
This trigger goes into action whenever a change happens on a specified branch in the depot. In our example, we want the trigger to fire whenever there is a change committed to the //depot/ShooterGame branch. The trigger will call a shell script (“call_ado_pipeline.sh”) that will send a REST call to Azure DevOps to activate a pipeline. 

16.	First, find out the number of the Azure DevOps pipeline you just created. The easiest way is to go to Pipelines, then click on your pipeline. The browser URL bar will display something like 

```
https://dev.azure.com/gamestudio/ShooterGameBuildPipeline/_build?definitionId=1
```

The number after definitionId is your pipeline’s serial number; note it for the next step.


17.	On your dev workstation, with your favorite code editor, create a text file “call_ado_pipeline.sh” with the following content:

```sh
#!/bin/sh

curl -u <PAT token> -X POST https://dev.azure.com/<your ADOName>/<yourProjectName>/_apis/pipelines/<pipelineNumber>/runs?api-version=6.0-preview.1 -H "Content-Type: application/json" -d '{"variables":{"CHANGESET":{"isSecret":false,"value":"'$1'"}}}'
```

16. Create a folder under the depot called triggers and save the file there.

[![Trigger setup 1](media/cloud-build-pipeline/acb6-azdopipeline/p4triggers1.png)](media/cloud-build-pipeline/acb6-azdopipeline/p4triggers1.png)

17.	Open p4v and refresh your workspace. You should see the triggers folder appear in the workspace. Right-click on it and Mark for add…   then right-click again and Submit…

18.	Your depot should now contain the trigger as follows:

[![Trigger setup 2](media/cloud-build-pipeline/acb6-azdopipeline/p4triggers2.png)](media/cloud-build-pipeline/acb6-azdopipeline/p4triggers2.png)

19.	SSH login to your Helix Core commit server as the administrative user, centos.  You can use something like PuTTY, or a more secure way like an Azure Bastion host. Either way, make sure you have your SSL key handy.

20.	Become the perforce user with 

```
su – perforce
(enter your password, which is the Helix Core Instance ID)
```

21.	Edit the p4 triggers file.

```
p4triggers
``` 

22.	The default editor, vi, starts up with the triggers file. 
- Go to the bottom of the file   <Shift-G>
- Type ‘o’ to enter insert mode on a new line.
- Hit tab 
- Copy this text and place it in the file:

```sh
call_ado change-commit //depot/ShooterGame/… “%//depot/triggers/call_ado_pipeline.sh% %change%”
```

Your file should now look similar to this: 

[![Trigger setup 3](media/cloud-build-pipeline/acb6-azdopipeline/p4triggers3.png)](media/cloud-build-pipeline/acb6-azdopipeline/p4triggers3.png)

23.	Save and exit with “<esc> : wq”

For more info on perforce triggers, please see the following links.

[Creating Perforce triggers](https://www.perforce.com/manuals/p4sag/Content/P4SAG/scripting.trigger.creating.html)

[Scripting and storing Perforce triggers](https://www.perforce.com/manuals/p4sag/Content/P4SAG/scripting.triggers.basics.html#Storing)



## Next steps

Next, go to Section 7: [Testing](./azurecloudbuilds-7-testing.md).

Or return to the [Introduction](./azurecloudbuilds-0-intro.md).

Troubleshooting page is [here](./azurecloudbuilds-9-troubleshooting.md).