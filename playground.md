
## ALL Function Integrated

```
function CreateDocument{
	param(
	[string]$issueId,
	[array]$assignees,
	[array]$members,
	[string]$documentType,
	[string]$project,
	[string]$documentShortTitle,
	[string]$sharedText
	
	)	
	$assign = ''
	$members = ''
	$debug = 1
	$project_name = "/Brake/" + $project

	$assign = '"' + ($assignees -join '","') + '"'

	$member = '"' + ($members -join '","') + '"'

if($debug -eq 1){
	Write-Output "issueId: $issueId"
	Write-Output "project name: $project_name"
	Write-Output "assign: $assign"
	Write-Output "notified: $members"
	Write-Output "Document Type: $documentType"
	Write-Output "Short Title: $documentShortTitle"
	Write-Output "Shared Text: $sharedText"
	
	im createcontent `
	--field="Project"=$project_name `
	--field="Document Type"="$documentType" `
	--addFieldValues="Assigned User"=$assign `
	--addFieldValues="Members To Be Notified"=$member `
	--field="Document Short Title"=$documentShortTitle `
	--field="Shared Text"=$sharedText
	}
}

function RM-Link{
	param(
	[string]$sourceId,
	[string]$targetId,
	[switch]$Verbose
	)
	
	$workdir = "C:\Users\system2.pm\Documents\RM"
	
	Write-Output "Running RM macro v0.1... by AJH"
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
	$server = "10.110.11.92"
	$port = "7001"
	$project = "/All_Customer_Requirements_Management/FORD/project.pj"
	$fileName = "Associated Test Methods_Evaluation Methods/[SYS1]_CRS_" + $fileName
	
	#fileName ex) TM-00.06-L-1062_NETCOM ECU LEVEL DIAGNOSTIC VERIFICATION_Rev14.pdf



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


function Standby{
	param(
	[string]$targetId,
	[string]$state,
	[array]$assignees,
	[array]$confirmers,
	[string]$comments,
	[boolean]$ctype
	)	
	$assign = ''
	$confirm = ''
	$debug = 1


	$assign = '"' + ($assignees -join '","') + '"'

	$confirm = '"' + ($confirmers -join '","') + '"'

if($debug -eq 1){
	Write-Output "target: $targetId"
	Write-Output "state: $state"
	Write-Output "comments: $comments"
	
	Write-Output "assign: $assign"
	Write-Output "confirm: $confirm"
	Write-Output "ctype: $ctype"
	
	$count = $confirmers.count
	if ($count){
		if ($ctype){
			im editissue `
			--field="State"=$state `
			--field="Comments"="$comments" `
			--addFieldValues="Assigned User"=$assign `
			--field="Confirmation Required ?"=true `
			--field="Members of Confirmation"=$confirm `
			--batchEdit $targetId
		}
		# Confirmers Exist
		im editissue `
		--field="State"=$state `
		--field="Comments"="$comments" `
		--addFieldValues="Assigned User"=$assign `
		--field="Confirmation Required ?"=true `
		--addFieldValues="Members of Confirmation"=$confirm `
		--batchEdit $targetId
		}
	
	else{
	# Confirmers doesn't exist
			im editissue `
		--field="State"=$state `
		--field="Comments"="$comments" `
		--addFieldValues="Assigned User"=$assign `
		--batchEdit $targetId
		}

	}
}


function setTimeEntry{
	param(
	[string]$targetId,
	[string]$type,
	[string]$duration
	)
	Write-Output "Attempting to set timeentry.. for target $targetId"

	$userName = "geonwoo.solution.pm"
	$autoDate = Get-Date -UFormat "%m/%d/%Y"

	if ($type -eq "edit"){
		im settimeentries --actionDefinition="<ActionList><Edit issueId='"$targetId"'user='"$userName"' entryDate='$autoDate'><Duration>$duration</Duration></Edit></ActionList>"
	}elseif ($type -eq "create"){
		im settimeentries --actionDefinition="<ActionList><Create issueId='"$targetId"'user='"$userName"' entryDate='$autoDate'><Duration>$duration</Duration></Create></ActionList>"
	}
	else{
	Write-Host "Type error"
	}
}



```


### RM Link Function
- : Extract Attatchments from base activity, upload it to the branched activity (creates work space)
```

function RM-Link{
	param(
	[string]$sourceId,
	[string]$targetId,
	[switch]$Verbose
	)
	
	$workdir = "C:\Users\system2.pm\Documents\RM"
	
	Write-Output "Running RM macro v0.1... by AJH"
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
	$server = "10.110.11.92"
	$port = "7001"
	$project = "/All_Customer_Requirements_Management/FORD/project.pj"
	$fileName = "Associated Test Methods_Evaluation Methods/[SYS1]_CRS_" + $fileName
	
	#fileName ex) TM-00.06-L-1062_NETCOM ECU LEVEL DIAGNOSTIC VERIFICATION_Rev14.pdf



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


### extractissue
----
- Extracts all attachments from the given activity (issue) to the current directory
```
im extractattachments --issue=$sourceId
// extracts all attachment to current directory
```

### createcontent
---
#### Playground:
```
im createcontent --field="Project"=/Brake/HKMC_Requirement --field="Document Type"="Customer requirements specification" --field="Document Short Title"="[SYS1]_CRS_ES95411-00" --field="Shared Text"="1. Customer requirements from HKMC_Requirement project.

2. Valid field

a. Section, ID, Category, Category Detail, Object Text, Customer's Feedback, Customer Req ID, ASIL, FTTI, Response, Responsible, Response Status by Supplier, Customer's Conclusion, Reference Mode, State

3. Field for review comments

a. Comment2 : Review comments of software team  
b. Comment3 : Review comments of system team  
c. Comment4 : Review comments of test team   
d. Comment spare 5: Review comments of the others"

```

```
function CreateDocument{
	param(
	[string]$issueId,
	[array]$assignees,
	[array]$members,
	[string]$documentType,
	[string]$project,
	[string]$documentShortTitle,
	[string]$sharedText
	
	)	
	$assign = ''
	$members = ''
	$debug = 1
	$project_name = "/Brake/" + $project

	$assign = '"' + ($assignees -join '","') + '"'

	$member = '"' + ($members -join '","') + '"'

if($debug -eq 1){
	Write-Output "issueId: $issueId"
	Write-Output "project name: $project_name"
	Write-Output "assign: $assign"
	Write-Output "notified: $members"
	Write-Output "Document Type: $documentType"
	Write-Output "Short Title: $documentShortTitle"
	Write-Output "Shared Text: $sharedText"
	
	im createcontent `
	--field="Project"=$project_name `
	--field="Document Type"="$documentType" `
	--addFieldValues="Assigned User"=$assign `
	--addFieldValues="Members To Be Notified"=$member `
	--field="Document Short Title"=$documentShortTitle `
	--field="Shared Text"=$sharedText
	}
}

```
 CHAR(34), ,CHAR(34),

CreateDocument


#### editissue
- Edits Activity/ Issue with given parameters
---
```
im editissue --addAttachment="field=Attachments,path=$absolutePath" $documentId

im editissue `
--field="State"=Standby `
--field="Comments"='-Updated State to "Standby"' `
--addFieldValues="Assigned User"="geonwoo.solution.cm" `
--field="Confirmation Required ?"=true `
--addFieldValues="Members of Confirmation"="geonwoo.solution.cm" `
--batchEdit 

More info:
https://support.ptc.com/help/windchillrvs/r12.3.0.0/en/index.html#page/IntegrityHelp%2Fim_editissue.html%23
```

## Standby Function (edit)
---
```
function Standby{
	param(
	[string]$targetId,
	[string]$state,
	[array]$assignees,
	[array]$confirmers,
	[string]$comments,
	[boolean]$ctype
	)	
	$assign = ''
	$confirm = ''
	$debug = 1


	$assign = '"' + ($assignees -join '","') + '"'

	$confirm = '"' + ($confirmers -join '","') + '"'

if($debug -eq 1){
	Write-Output "target: $targetId"
	Write-Output "state: $state"
	Write-Output "comments: $comments"
	
	Write-Output "assign: $assign"
	Write-Output "confirm: $confirm"
	Write-Output "ctype: $ctype"
	
	$count = $confirmers.count
	if ($count){
		if ($ctype){
			im editissue `
			--field="State"=$state `
			--field="Comments"="$comments" `
			--addFieldValues="Assigned User"=$assign `
			--field="Confirmation Required ?"=true `
			--field="Members of Confirmation"=$confirm `
			--batchEdit $targetId
		}
		# Confirmers Exist
		im editissue `
		--field="State"=$state `
		--field="Comments"="$comments" `
		--addFieldValues="Assigned User"=$assign `
		--field="Confirmation Required ?"=true `
		--addFieldValues="Members of Confirmation"=$confirm `
		--batchEdit $targetId
		}
	
	else{
	# Confirmers doesn't exist
			im editissue `
		--field="State"=$state `
		--field="Comments"="$comments" `
		--addFieldValues="Assigned User"=$assign `
		--batchEdit $targetId
		}

	}
}

```

#### EXCEL Ver
```
=CONCATENATE("Standby -targetId ",$B$7," -state ", CHAR(34), $C$10,CHAR(34)," -comments ",$F$10," -assignees ",CHAR(34),$B$14,CHAR(34),IF(NOT(COUNTBLANK($C$14)), CONCATENATE(", ", CHAR(34), $C$14, CHAR(34)),""), IF(NOT(COUNTBLANK($D$14)), CONCATENATE(", ", CHAR(34), $D$14, CHAR(34)),""), IF(NOT(COUNTBLANK($F$14)), CONCATENATE(" -confirmers ", CHAR(34), $F$14, CHAR(34)),""), IF(NOT(COUNTBLANK($G$14)), CONCATENATE(", ", CHAR(34), $G$14, CHAR(34)),""), IF(NOT(COUNTBLANK($H$14)), CONCATENATE(", ", CHAR(34), $H$14, CHAR(34)),""), CONCATENATE(", -Ctype=", CHAR(34), $E$14, CHAR(34)), ";")
```

#### Excel (Old)
```
=CONCATENATE("im editissue", " --field=", CHAR(34), "State", CHAR(34),"=", $C13, " --field=", CHAR(34), "Confirmation Required ?", CHAR(34),"=", $D13, " --addFieldValues=", CHAR(34), "Assigned User", CHAR(34),"=",  CHAR(34), $B13,  CHAR(34), IF(NOT(ISBLANK($B14)), CONCATENATE(" --addFieldValues=", CHAR(34), "Assigned User", CHAR(34),"=",  CHAR(34), $B14,  CHAR(34)), ""), IF(NOT(ISBLANK($B15)), CONCATENATE(" --addFieldValues=", CHAR(34), "Assigned User", CHAR(34),"=",  CHAR(34), $B15,  CHAR(34)), ""), IF(NOT(ISBLANK($B16)), CONCATENATE(" --addFieldValues=", CHAR(34), "Assigned User", CHAR(34),"=",  CHAR(34), $B16,  CHAR(34)), ""), IF($F13="Add"," --addFieldValues=", "--field="), CHAR(34), "Members of Confirmation", CHAR(34),"=",  CHAR(34), $E13,  CHAR(34), IF(NOT(ISBLANK($E14)), CONCATENATE(" --addFieldValues","=", CHAR(34), "Members of Confirmation", CHAR(34),"=",  CHAR(34), $E14,  CHAR(34)), ""), IF(NOT(ISBLANK($E15)), CONCATENATE(" --addFieldValues=", CHAR(34), "Members of Confirmation", CHAR(34),"=",  CHAR(34), $E15,  CHAR(34)), ""), IF(NOT(ISBLANK($E16)), CONCATENATE(" --addFieldValues=", CHAR(34), "Members of Confirmation", CHAR(34),"=",  CHAR(34), $E16,  CHAR(34)), ""), "--field=", CHAR(34), "Comments", CHAR(34),"=", $H13, " --batchEdit ", G13, ";")
```


### Get abs path and file name for current directory
- Get children within directory, and loop over the object // doing something
```
Get-ChildItem -File | ForEach-Object {
	# absolute path
	$absolutePath = $_.FullName
	
	$fileName = $_.Name
	
	Write-Output "path: $absolutePath / name: $fileName"
	im editissue --addAttachment="field=Attachments,path=$absolutePath" 11071264
}

```



### EditSourceTraceLink
---
#### Source Code
```
Source trace link

$fieldName = "Source Trace"
$server = "10.110.11.92"
$port = "7001"
$project = "/All_Customer_Requirements_Management/FORD/project.pj"
$fileName = "Associated Test Methods_Evaluation Methods/[SYS1]_CRS_" + $fileName

#fileName ex) TM-00.06-L-1062_NETCOM ECU LEVEL DIAGNOSTIC VERIFICATION_Rev14.pdf

im editissue --addSourceTrace="field=$fieldName,server=$server,port=$port,file=$fileName,project=$project" $targetId
```


### SetTimeEntries
---
#### playground
```
$autoDate = Get-Date -UFormat "%m/%d/%Y"


Get-Date -Format "MM/dd/yyyy" // 08-02-2024
Get-Date -UFormat "%m/%d/%Y" // 08/02/2024


im settimeentries --actionDefinition=<ActionList>  
<Create issueId="$targetId" user="$userName" entryDate="$autoDate">  
<Duration>.1</Duration>  
</Create>  
</ActionList>
```

#### TimeEntry Function
```
function setTimeEntry{
	param(
	[string]$targetId,
	[string]$type,
	[string]$duration
	)
	Write-Output "Attempting to set timeentry.. for target $targetId"

	$userName = "geonwoo.solution.pm"
	$autoDate = Get-Date -UFormat "%m/%d/%Y"

	if ($type -eq "edit"){
		im settimeentries --actionDefinition="<ActionList><Edit issueId='"$targetId"'user='"$userName"' entryDate='$autoDate'><Duration>$duration</Duration></Edit></ActionList>"
	}elseif ($type -eq "create"){
		im settimeentries --actionDefinition="<ActionList><Create issueId='"$targetId"'user='"$userName"' entryDate='$autoDate'><Duration>$duration</Duration></Create></ActionList>"
	}
	else{
	Write-Host "Type error"
	}
}
```
#### Excel Ver:

```
=CONCATENATE("setTimeEntry ", $B$7, " ", $D$7, " ", $C$7)
=CONCATENATE("$autoDate = Get-Date -UFormat ",CHAR(34), "%m/%d/%Y", CHAR(34), ";")
```

