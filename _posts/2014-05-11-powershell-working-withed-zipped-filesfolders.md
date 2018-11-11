---
ID: 97
post_title: 'PowerShell &#8211;  Working withed zipped files/folders'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-working-withed-zipped-filesfolders
published: true
post_date: 2014-05-11 00:00:00
---
Special Chapter - unzipping zipped files using Powershell

Note: one of the easiest way to worked with compressed tar files is by using the pscx module:

http://pscx.codeplex.com/


######## detour - start
FOund this bit of code on the internet:

function Extract-Zip
{
	param([string]$zipfilename, [string] $destination)

	if(test-path($zipfilename))
	{	
		$shellApplication = new-object -com shell.application
		$zipPackage = $shellApplication.NameSpace($zipfilename)
		$destinationFolder = $shellApplication.NameSpace($destination)
		$destinationFolder.CopyHere($zipPackage.Items())
	}
}

extract-zip filename.zip unzippedfilename
######## detour - end 


######## Another detrou - start
# Code this code from:
# http://sushihangover.blogspot.co.uk/2013/03/powershell-gzip-gz-compress-and.html#!/2013/03/powershell-gzip-gz-compress-and.html
# Got the initialize-file (and touch-file) function from:
# https://github.com/sushihangover


Set-StrictMode ×–ersion latest
function Initialize-File{
    <#
        .NOTES
            Copyright 2013 Robert Nees
            Licensed under the Apache License, Version 2.0 (the "License");
            http://sushihangover.blogspot.com
        .SYNOPSIS
            touch-file -- change file access and modification times
        .DESCRIPTION
         The touch utility sets the modification and access times of files.  If any file does not exist, 
         it is created with default permissions. (see examples)
     
            -a (AccessTime) Change just the access time of the file.
            -c (Create) Do not create the file if it does not exist.  The touch utility does not treat 
                this as an error.  No error messages are displayed and the exit value is not affected.
            -f (Force) Attempt to force the update, even if the file permissions do not currently permit 
                it. FYI: Only valid on file creation!
            -m (ModificationTime) Change just the modification time of the file.
            -n (CreationTime) Change just the creation time of the file (when it was 'n'ew).
            -r (Replace) Use the access and modifications times from the specified file instead of the 
                current time of day.
            -t (Time) Change the access and modification times to the specified time instead of the 
                current time of day.  The argument is of the form of a .Net DateTime string
        .EXAMPLE
            TODO : Add Examples
        .LINK
            http://sushihangover.blogspot.com
    #>
    [cmdletbinding(SupportsShouldProcess=$True)]
    Param(
        [Parameter(Mandatory=$true,ValueFromPipeline=$True)][String]$FileName,
        [Parameter(Mandatory=$false)][Alias('r')][String]$Replace = "",
        [Parameter(Mandatory=$false)][Alias('t')][String]$Time = "",
        [Parameter(Mandatory=$false)][Alias('c')][Switch]$Create,
        [Parameter(Mandatory=$false)][Alias('a')][Switch]$AccessTime,
        [Parameter(Mandatory=$false)][Alias('m')][Switch]$ModificationTime,
        [Parameter(Mandatory=$false)][Alias('n')][Switch]$CreationTime,
        [Parameter(Mandatory=$false)][Alias('f')][Switch]$Force
    )
    begin {
        function Update-FileSystemInfo([System.IO.FileSystemInfo]$fsInfo) {
            if ($Time -ne $null) {
                $fsInfo.CreationTime = $CurrentDateTime
                $fsInfo.LastWriteTime = $CurrentDateTime
                $fsInfo.LastAccessTime = $CurrentDateTime
            } else {
                if ($AccessTime.IsPresent) {
                    $fsInfo.LastAccessTime = $CurrentDateTime
                }
                if ($ModificationTime.IsPresent) {
                    $fsInfo.LastWriteTime = $CurrentDateTime
                }
                if ($CreationTime.IsPresent) {
                    $fsInfo.CreationTime = $CurrentDateTime
                }
            }
        }
   
        function Touch-NewFile {
            [cmdletbinding(SupportsShouldProcess=$True)]
            Param(
                [Parameter(Mandatory=$True)][String]$FileName
            )
            if ($Force.IsPresent ) {
                Set-Content -Path ($FileName) -Value ($null) -Force
            } else {
                Set-Content -Path ($FileName) -Value ($null)
            }
            $fsInfo = new-object System.IO.FileInfo($FileName)
            return $fsInfo
        }

        if ($Replace -ne "") {
            try {
                $replaceInfo = Get-ChildItem $Replace
                $CurrentDateTime = $replaceInfo.CreationTime
            } catch {
                return
            }
        } else {
            if ($Time -ne "") {
                $CurrentDateTime = [DateTime]::Parse($Time)
            } else {
                $CurrentDateTime = Get-Date            
            }
        }
    }
    process {
        if ($pscmdlet.ShouldProcess($FileName)) {
            if (test-path $FileName) {
                $fsInfo = Get-ChildItem $FileName
                Update-FileSystemInfo($fsInfo)
            }
            else {
                if (!$Create.IsPresent) {
                    $fsInfo = Touch-NewFile($FileName)
                    $fsInfo = Get-ChildItem $FileName
                    Update-FileSystemInfo($fsInfo)
                }
            }
        }
        $fsInfo = $null
    }
    end {
    }
}

# Initialize-File naming sucks for the 'touch' command but makes sense in the 
# verb list and passes loading without errors, but lets alias to 'touch-file', ok!
# Not setting alias to 'touch' to avoid 'hidding' your cygwn touch.exe, etc..., do 
# that in your profile if you are not using another version of Touch on your system.
Set-Alias Touch-File Initialize-File -Scope Global
 
 
 <#
.NOTES
    Copyright 2013 Robert Nees
    Licensed under the Apache License, Version 2.0 (the "License");
.SYNOPSIS
    GZip Compress and DeCompress
.DESCRIPTION
    A 8k buffered GZip (.gz) Compress and DeCompress functions that support pipelined input
.LINK
    http://sushihangover.blogspot.com
.LINK
    https://github.com/sushihangover
#>
function Compress-GZip {
    <#
    .NOTES
        Copyright 2013 Robert Nees
        Licensed under the Apache License, Version 2.0 (the "License");
    .SYNOPSIS
        GZip Compress (.gz)
    .DESCRIPTION
        A buffered GZip (.gz) Compress function that support pipelined input
    .Example
        ls .\NotCompressFile.xml | Compress-GZip -Verbose -WhatIf
    .Example
        Compress-GZip -FullName NotCompressFile.xml -NewName Compressed.xml.funkyextension
    .LINK
        http://sushihangover.blogspot.com
    .LINK
        https://github.com/sushihangover
    #>
    [cmdletbinding(SupportsShouldProcess=$True,ConfirmImpact="Low")]
    param (
        [Alias("PSPath")][parameter(mandatory=$true,ValueFromPipeline=$true,ValueFromPipelineByPropertyName=$true)][string]$FullName,
        [Alias("NewName")][parameter(mandatory=$false,ValueFromPipeline=$false,ValueFromPipelineByPropertyName=$true)][string]$GZipPath,
        [parameter(mandatory=$false)][switch]$Force
    )
    Process {
        $_BufferSize = 1024 * 8
        if (Test-Path -Path $FullName -PathType Leaf) {
            Write-Verbose "Reading from: $FullName"
            if ($GZipPath.Length -eq 0) {
                $tmpPath = ls -Path $FullName
                $GZipPath = Join-Path -Path ($tmpPath.DirectoryName) -ChildPath ($tmpPath.Name + '.gz')
            }
            if (Test-Path -Path $GZipPath -PathType Leaf -IsValid) {
                Write-Verbose "Compressing to: $GZipPath"
            } else {
                Write-Error -Message "$FullName is not a valid path/file"
                return
            }
        } else {
            Write-Error -Message "$GZipPath does not exist"
            return
        }
        if (Test-Path -Path $GZipPath -PathType Leaf) {
            If ($Force.IsPresent) {
                if ($pscmdlet.ShouldProcess("Overwrite Existing File @ $GZipPath")) {
                    Touch-File $GZipPath
                }
            }
        } else {
            if ($pscmdlet.ShouldProcess("Create new Compressed File @ $GZipPath")) {
                Touch-File $GZipPath
            }
        }
        if ($pscmdlet.ShouldProcess("Creating Compress File @ $GZipPath")) {
            Write-Verbose "Opening streams and file to save compressed version to..."
            $input = New-Object System.IO.FileStream (ls -path $FullName).FullName, ([IO.FileMode]::Open), ([IO.FileAccess]::Read), ([IO.FileShare]::Read);
            $output = New-Object System.IO.FileStream (ls -path $GZipPath).FullName, ([IO.FileMode]::Create), ([IO.FileAccess]::Write), ([IO.FileShare]::None)
            $gzipStream = New-Object System.IO.Compression.GzipStream $output, ([IO.Compression.CompressionMode]::Compress)
            try {
                $buffer = New-Object byte[]($_BufferSize);
                while ($true) {
                    $read = $input.Read($buffer, 0, ($_BufferSize))
                    if ($read -le 0) {
                        break;
                    }
                    $gzipStream.Write($buffer, 0, $read)
                }
            }
            finally {
                Write-Verbose "Closing streams and newly compressed file"
                $gzipStream.Close();
                $output.Close();
                $input.Close();
            }
        }
    }
}
function Expand-GZip {
    <#
    .NOTES
        Copyright 2013 Robert Nees
        Licensed under the Apache License, Version 2.0 (the "License");
    .SYNOPSIS
        GZip Decompress (.gz)
    .DESCRIPTION
        A buffered GZip (.gz) Decompress function that support pipelined input
    .Example
        ls .\RegionName.cs.gz | Expand-GZip -Verbose -WhatIf
    .Example
        Expand-GZip -FullName CompressFile.xml.gz -NewName NotCompressed.xml
    .LINK
        http://sushihangover.blogspot.com
    .LINK
        https://github.com/sushihangover
    #>
    [cmdletbinding(SupportsShouldProcess=$True,ConfirmImpact="Low")]
    param (
        [Alias("PSPath")][parameter(mandatory=$true,ValueFromPipeline=$true,ValueFromPipelineByPropertyName=$true)][string]$FullName,
        [Alias("NewName")][parameter(mandatory=$false,ValueFromPipeline=$false,ValueFromPipelineByPropertyName=$true)][string]$GZipPath = $null,
        [parameter(mandatory=$false)][switch]$Force
    )
    Process {
        if (Test-Path -Path $FullName -PathType Leaf) {
            Write-Verbose "Reading from: $FullName"
            if ($GZipPath.Length -eq 0) {
                $tmpPath = ls -Path $FullName
                $GZipPath = Join-Path -Path ($tmpPath.DirectoryName) -ChildPath ($tmpPath.BaseName)
            }
            if (Test-Path -Path $GZipPath -PathType Leaf -IsValid) {
                Write-Verbose "Decompressing to: $GZipPath"
            } else {
                Write-Error -Message "$GZipPath is not a valid path/file"
                return
            }
        } else {
            Write-Error -Message "$FullName does not exist"
            return
        }
        if (Test-Path -Path $GZipPath -PathType Leaf) {
            If ($Force.IsPresent) {
                if ($pscmdlet.ShouldProcess("Overwrite Existing File @ $GZipPath")) {
                    Touch-File $GZipPath
                }
            }
        } else {
            if ($pscmdlet.ShouldProcess("Create new decompressed File @ $GZipPath")) {
                Touch-File $GZipPath
            }
        }
        if ($pscmdlet.ShouldProcess("Creating Decompressed File @ $GZipPath")) {
            Write-Verbose "Opening streams and file to save compressed version to..."
            $input = New-Object System.IO.FileStream (ls -path $FullName).FullName, ([IO.FileMode]::Open), ([IO.FileAccess]::Read), ([IO.FileShare]::Read);
            $output = New-Object System.IO.FileStream (ls -path $GZipPath).FullName, ([IO.FileMode]::Create), ([IO.FileAccess]::Write), ([IO.FileShare]::None)
            $gzipStream = New-Object System.IO.Compression.GzipStream $input, ([IO.Compression.CompressionMode]::Decompress)
            try {
                $buffer = New-Object byte[](1024);
                while ($true) {
                    $read = $gzipStream.Read($buffer, 0, 1024)
                    if ($read -le 0) {
                        break;
                    }
                    $output.Write($buffer, 0, $read)
                }
            }
            finally {
                Write-Verbose "Closing streams and newly decompressed file"
                $gzipStream.Close();
                $output.Close();
                $input.Close();
            }
        }
    }
}
Expand-GZip -FullName vmlin258_17_out.tar.gz -NewName vmlin258_17_out.tar
######## another dtoru - end


The best way to to see this in action by following the following example:

In this example we look in a folder, and in this folder we get a list of all the zip files, on which we 

Get-ChildItem C:\path\to\folder\containing\one\or\more\zip\files\*.zip | ForEach-Object {
    
    $FileName = $_.Name				# this houses the name of the zip file. 
    $FullZippedFileName = "$TargetFolder\$component\$FileName"    # this again is the zipfile's name but also includes the full path. 
    $TargetFolderForUnzippedFiles = "$TargetFolder\$component"    # this is where the unzipped contain will get sent to. 
        
    $shell_app=new-object -com shell.application		# here we create a generic shell.application object. need to search for "shell.applicaton"
														# in powershell books. 
    
    $zip_file = $shell_app.namespace("$FullZippedFileName")             # Not sure what happens here, but I think we are telling the unzipping object
																		# where the zipfile is so that it can locate it. 
																		
    $destination = $shell_app.namespace($TargetFolderForUnzippedFiles)	# here we are saying where to store the extracted files too. 
	
    $destination.Copyhere($zip_file.items())							# this initiates the actual unzipping process. 

}


Also see:

http://serverfault.com/questions/18872/how-to-zip-unzip-files-in-powershell

Here is also a very useful script that you can use to pull out certain files out of the zip file, based on their file/folder name:

http://gallery.technet.microsoft.com/scriptcenter/PowerShell-Get-Specific-9b35352f

I used this script to pull out sql scripts out of release folder versions, when they were missing from my chosen component version folder.