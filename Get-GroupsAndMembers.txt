<#
.SYNOPSIS
  Use this script to find All Groups and Then Get a List of all the Members of the Groups. 
.DESCRIPTION
  Export Group Members into CSVs
.PARAMETER <Parameter_Name>
    <Brief description of parameter input required. Repeat this attribute if required>
.INPUTS
  <Inputs if any, otherwise state None>
.OUTPUTS
  <Outputs if any, otherwise state None - example: Log file stored in C:\Windows\Temp\<name>.log>
.NOTES
  Version:        1.0
  Author:         Ed Buford
  Creation Date:  9/30/2021
  License: Creative Commons
  
.EXAMPLE
  On A Domain Controller - run:    PS:C:\source ./Get-Groups.ps1
#>

Import-Module ActiveDirectory

#Test if Folder Exists

$FolderName = "C:\source\Groups"
If (!(test-path $FolderName)) {

Write-Host "Creating Folder to put the files in" -Foregroundcolor Green
md $FolderName

}

#Test for file to see if it exists and if so delete it. 

$FileName = "c:\source\Groups\ADGroups.csv"
if (Test-Path $FileName) {
  Remove-Item $FileName
}

#Grab all the Groups and dump them to a csv

$ADG = get-adgroup -filter * -Properties * | select * | Export-csv c:\source\Groups\ADGroups.csv -notypeinformation

#import csv into a variable. 

$ADGroups = Import-csv -path "C:\source\Groups\ADGroups.csv"

#Loop through the groups 

Foreach ($Group in $ADGroups) {

$GroupName = $Group.SamaccountName


$path = "C:\Source\Groups\" + $GroupName + ".csv"
Write-host "Creating Repoort for " $GroupName -Foregroundcolor Green
Get-AdGroupMember -identity $GroupName |Export-CSV -path $path -NoTypeInformation

}
