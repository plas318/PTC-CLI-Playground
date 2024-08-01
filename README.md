# PTC-CLI-Playground
Repository to practice the cli automation of the PTC windchill RTS v12.3.0, via powershell bash script



### Prototype function Test
- Automation Script for Tracing Attachments from the branched source(original) to the target source(new source) or the source document to the target document

- Params include sourceId, targetId, and a Verbose option for some simple.. debug text.!

```
function RM-Link{
	param(
	[string]$sourceId,
	[string]$targetId,
	[switch]$Verbose
	)
	
	$workdir = "C:\Users\system2.pm\Documents\RM"
	
	Write-Output "Running RM macro v0.1... by JH"
	cd $workdir
	
	# create workspace
	if ($Verbose){
		Write-Output "Creating workspace... $workdir"
	}

	if (Test-Path -Path $sourceId){
	Write-Output "workspace exists.. skipping"
	} else {
		mkdir $sourceId
	}
	
	
	# move to workspace
	if ($Verbose){
		Write-Output "Changing workspace... to $workdir"
	}
	cd $sourceId
	
	# download all attachments
	if ($Verbose){
		Write-Output "Downloading files..."
	}
	im extractattachments --issue=$sourceId
	
	
	Get-ChildItem -File | ForEach-Object {
	# absolute path
	$absolutePath = $_.FullName
	
	$fileName = $_.Name
	
	if ($Verbose){
		# Write-Output "path: $absolutePath / name: $fileName"
	}
	

	$fieldName = "Source Trace"
	$server = "10.110.x.x"
	$port = "7001"
	$project = "/All_Customer_Requirements_Management/FORD/project.pj"
	$fileName = "Associated Test Methods_Evaluation Methods/[SYS1]_CRS_" + $fileName



	im editissue --addAttachment="field=Attachments,path=$absolutePath" $targetId
	try{
	im editissue --addSourceTrace="field=$fieldName,server=$server,port=$port,file=$fileName,project=$project" $targetId
	}
	catch{
		Write-Output "Failed to create a Source Trace Link to: $fileName, skipping.."
	}
	
	}
Write-Output "Sucessfully Completed Task..!"
}
```


## Breakdown Detailed Components
---

### Get Absolute path and Filename
```
Get-ChildItem -File | ForEach-Object {
# Get all files within the local directory and save the $filename, and $abspath
$absolutePath = $_.FullName
$fileName = $_.Name

# For Debug
Write-Output "path: $absolutePath / name: $fileName"
}
```

### editissue
```
# editissue takes the document/issue/activity's id and edits the doc with various options

im editissue [--[no]showWorkflow] [--[no]batchEdit] [--query=[user:]query] [--removeAttachment=value] [--removeFieldValues=value] [--removeRelationships=value] [--addAttachment=value] [--addFieldValues=value] [--updateAttachment=value] [--addRelationships=value] [--addSourceTrace=value] [--addSourceLink=value] [--removeSourceTrace=value] [--removeSourceLink=value] [--customFieldDefinition=value] [--customFieldValue=value] [--removeCustomFieldDefinition=name] [--field=value] [--richContentField=value] [--hostname=value] [--port=value] [--password=value] [--user=value] [(-?|--usage)] [(-g|--gui)] [(-F value|--selectionFile=value)] [--quiet] [--settingsUI=[gui|default]] [--status=[none|gui|default]] [(-N|--no)] [(-Y|--yes)] [--[no]batch] [--cwd=value] [--forceConfirm=[yes|no]] issue id.

ex) im editissue --addAttachment="field=Attachments,path=$absolutePath" $documentId
```

#### editissue -addAttachment
```
--addAttachment="field=Attachments,path=$absolutePath" $documentId
```

#### editissue -addSourceTrace
```
$fieldName = "Source Trace"
$server = "10.110.x.x"
$port = "7001"
$project = "/projectname/subfolder/project.pj"
$header = "[SYS1]_CRS_"
$fileName = "$folder_name/$header" + $fileName

#fileName ex) name_Rev14.pdf

im editissue --addSourceTrace="field=$fieldName,server=$server,port=$port,file=$fileName,project=$project" $targetId
```




```

### extractissue
```
# extracts all attachments from the source document, and downloads it to the current working directory
im extractattachments --issue=$sourceId

```
