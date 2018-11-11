---
ID: 91
post_title: 'PowerShell &#8211; Troubleshooting pipeline input'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-troubleshooting-pipeline-input
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 19 - Troubleshooting pipeline input

In this chapter we will learn about how to do troubleshooting (in this case relating to pipelines) with the help of a command called "trace-command"

Lets say we have a csv file with only one column, containing the the following rows:

Computername
localhost

Now you get the following output if you do:

PS C:\> Import-Csv -Path .\computers.csv

Computername
------------
localhost


Now let's say we want to get localhost's machine's bios info by piping in "localhost" into the "-computername" parameter of:

Get-WmiObject -ComputerName {piped in data} -Class win32_bios

To achieve this we try:


PS C:\>  Import-Csv -Path .\computers.csv  |  Get-WmiObject -Class win32_bios

Unfortunately this fails to work. That's because if you look in:

help Get-WmiObject -full

You will find that Get-WmiObject command's "-computername" parameter cannot accept pipeline input. 


However it is not a good idea to alway's rely on the help pages to figure out what is wrong with your code. A better/more-sophisticated approach is to use the "trace-command". This is powershell's diagnostic/troubleshooting command. It is used to identify an problems in the code.  

Here is the trace-command's approach to identifying the problem:

PS C:\> Trace-Command -Name ParameterBinding -PSHost -Expression {Import-Csv -Path .\computers.csv  |  Get-WmiObject -Class win32_bios}


	The "-name" parameter is actually radio-button parameter. The available nameradio-options can be listed by running the following command:

	PS C:\> Get-TraceSource

	The -name parameter essentially lets you choose what behaviour of the code you want to analyse. 

	The -PSHost is a switch parameter, which you need to switch on if you want to view the debugging info onscreen. 

	Note, the trace-command tool will actually run the code that is in {...} as part of it's debugging activity. So be careful what you run in this. 

  
The output of the above trace-command is as follows:

#############################################################################################################################################

DEBUG: ParameterBinding Information: 0 : BIND NAMED cmd line args [Import-Csv]          
# To start with, ps tries to bind any parameters (that have been explicitly named) to import-csv command. Which in this case is the "-path" parameter.

DEBUG: ParameterBinding Information: 0 :     BIND arg [.\computers.csv] to parameter [Path] 
# first bind ".\computers.csv" to "-path" parameter. Notice that this line is indented. This means that it is a child element of the previous 
# parent command. This lines wouldn't have appeared if we didn't explicitly declare "-Path" in the code. This line is saying that 
# powershell is about to attempt the value ".\computers.csv" to the parameter "-Path".

DEBUG: ParameterBinding Information: 0 : BIND POSITIONAL cmd line args [Import-Csv]
DEBUG: ParameterBinding Information: 0 :     BIND arg [.\computers.csv] to parameter [Path]
# The above 2 lines didn't appear, but would have if I didn't explicitly declared "-path" in the code. I inserted the above 2 lines 
# to high-light the fact that Parameters are binded in the following order:
#			1. explicitly named parameters
#			2. implicitly (positional) parameters  

DEBUG: ParameterBinding Information: 0 :         COERCE arg to [System.String[]]
# At the moment our value ".\computers.csv" is a string (i.e. System.String). However the -path parameter is designed to only 
# accept arrays (i.e. System.String[]). So the next step is to persuade (i.e. "coerce") our value to change into an array, which is possible if
# powershell creates an array and adding our single value (i.e. ".\computers.csv") to that array.   
   

DEBUG: ParameterBinding Information: 0 :             Trying to convert argument value from System.String to System.String[]
DEBUG: ParameterBinding Information: 0 :             ENCODING arg into collection 
DEBUG: ParameterBinding Information: 0 :             Binding collection parameter Path: argument type [String], parameter type [System.String[]], collection type Array, element type [System.String], coerceElementType
DEBUG: ParameterBinding Information: 0 :             Creating array with element type [System.String] and 1 elements
DEBUG: ParameterBinding Information: 0 :             Argument type String is not IList, treating this as scalar
DEBUG: ParameterBinding Information: 0 :             COERCE arg to [System.String]
# Here we are going to (if required) persuade our value (".\computers.csv") to change into the data type that can be accepted 
# as an entry in the array.

DEBUG: ParameterBinding Information: 0 :                 Parameter and arg types the same, no coercion is needed.
# "string[]" is an array where the entries are supposed to be of the string data type. Since ".\computers.csv" is already a string, it means no
# coercion is required before adding the value to the array. 

DEBUG: ParameterBinding Information: 0 :             Adding scalar element of type String to array position 0
# Here we finally add our value as the first element in the array. 

DEBUG: ParameterBinding Information: 0 :         Executing VALIDATION metadata: [System.Management.Automation.ValidateNotNullOrEmptyAttribute]
# This is a check to see that the value we added to the array is not a null value. 

DEBUG: ParameterBinding Information: 0 :         BIND arg [System.String[]] to param [Path] SUCCESSFUL
# This confirms that the parameter binding has been s. 



DEBUG: ParameterBinding Information: 0 : BIND POSITIONAL cmd line args [Import-Csv]
DEBUG: ParameterBinding Information: 0 : MANDATORY PARAMETER CHECK on cmdlet [Import-Csv]
DEBUG: ParameterBinding Information: 0 : BIND NAMED cmd line args [Get-WmiObject]
DEBUG: ParameterBinding Information: 0 :     BIND arg [win32_bios] to parameter [Class]
DEBUG: ParameterBinding Information: 0 :         COERCE arg to [System.String]
DEBUG: ParameterBinding Information: 0 :             Parameter and arg types the same, no coercion is needed.
DEBUG: ParameterBinding Information: 0 :         BIND arg [win32_bios] to param [Class] SUCCESSFUL
DEBUG: ParameterBinding Information: 0 : BIND POSITIONAL cmd line args [Get-WmiObject]
DEBUG: ParameterBinding Information: 0 : MANDATORY PARAMETER CHECK on cmdlet [Get-WmiObject]
DEBUG: ParameterBinding Information: 0 : CALLING BeginProcessing
DEBUG: ParameterBinding Information: 0 : CALLING BeginProcessing
DEBUG: ParameterBinding Information: 0 : BIND PIPELINE object to parameters: [Get-WmiObject]
DEBUG: ParameterBinding Information: 0 :     PIPELINE object TYPE = [System.Management.Automation.PSCustomObject]
DEBUG: ParameterBinding Information: 0 :     RESTORING pipeline parameter's original values
DEBUG: ParameterBinding Information: 0 : BIND PIPELINE object to parameters: [Out-Default]
DEBUG: ParameterBinding Information: 0 :     PIPELINE object TYPE = [System.Management.Automation.ErrorRecord]
DEBUG: ParameterBinding Information: 0 :     RESTORING pipeline parameter's original values
DEBUG: ParameterBinding Information: 0 :     Parameter [InputObject] PIPELINE INPUT ValueFromPipeline NO COERCION
DEBUG: ParameterBinding Information: 0 :     BIND arg [The input object cannot be bound to any parameters for the command either because the command do
es not take pipeline input or the input and its properties do not match any of the parameters that take pipeline input.] to parameter [InputObject]
DEBUG: ParameterBinding Information: 0 :         BIND arg [The input object cannot be bound to any parameters for the command either because the command does not take pipeline input or the input and its properties do not match any of the parameters that take pipeline input.] to param [InputObject] SUCCESSFUL
DEBUG: ParameterBinding Information: 0 : MANDATORY PARAMETER CHECK on cmdlet [Out-Default]
DEBUG: ParameterBinding Information: 0 : BIND NAMED cmd line args [Out-LineOutput]
DEBUG: ParameterBinding Information: 0 :     BIND arg [Microsoft.PowerShell.Commands.Internal.Format.ConsoleLineOutput] to parameter [LineOutput]
DEBUG: ParameterBinding Information: 0 :         COERCE arg to [System.Object]
DEBUG: ParameterBinding Information: 0 :             Parameter and arg types the same, no coercion is needed.
DEBUG: ParameterBinding Information: 0 :         BIND arg [Microsoft.PowerShell.Commands.Internal.Format.ConsoleLineOutput] to param [LineOutput] SUCCESSFUL
DEBUG: ParameterBinding Information: 0 : BIND POSITIONAL cmd line args [Out-LineOutput]
DEBUG: ParameterBinding Information: 0 : MANDATORY PARAMETER CHECK on cmdlet [Out-LineOutput]
DEBUG: ParameterBinding Information: 0 : CALLING BeginProcessing
DEBUG: ParameterBinding Information: 0 : BIND PIPELINE object to parameters: [Out-LineOutput]
DEBUG: ParameterBinding Information: 0 :     PIPELINE object TYPE = [System.Management.Automation.ErrorRecord]
DEBUG: ParameterBinding Information: 0 :     RESTORING pipeline parameter's original values
DEBUG: ParameterBinding Information: 0 :     Parameter [InputObject] PIPELINE INPUT ValueFromPipeline NO COERCION
DEBUG: ParameterBinding Information: 0 :     BIND arg [The input object cannot be bound to any parameters for the command either because the command does not take pipeline input or the input and its properties do not match any of the parameters that take pipeline input.] to parameter [InputObject]
DEBUG: ParameterBinding Information: 0 :         BIND arg [The input object cannot be bound to any parameters for the command either because the comman
d does not take pipeline input or the input and its properties do not match any of the parameters that take pipeline input.] to param [InputObject] SUCCESSFUL
DEBUG: ParameterBinding Information: 0 : MANDATORY PARAMETER CHECK on cmdlet [out-lineoutput]
DEBUG: ParameterBinding Information: 0 : BIND NAMED cmd line args [Format-Default]
DEBUG: ParameterBinding Information: 0 : BIND POSITIONAL cmd line args [Format-Default]
DEBUG: ParameterBinding Information: 0 : MANDATORY PARAMETER CHECK on cmdlet [Format-Default]
DEBUG: ParameterBinding Information: 0 : CALLING BeginProcessing
DEBUG: ParameterBinding Information: 0 : BIND PIPELINE object to parameters: [Format-Default]
DEBUG: ParameterBinding Information: 0 :     PIPELINE object TYPE = [System.Management.Automation.ErrorRecord]
DEBUG: ParameterBinding Information: 0 :     RESTORING pipeline parameter's original values
DEBUG: ParameterBinding Information: 0 :     Parameter [InputObject] PIPELINE INPUT ValueFromPipeline NO COERCION
DEBUG: ParameterBinding Information: 0 :     BIND arg [The input object cannot be bound to any parameters for the command either because the command does not take pipeline input or the input and its properties do not match any of the parameters that take pipeline input.] to parameter [InputObject]
DEBUG: ParameterBinding Information: 0 :         BIND arg [The input object cannot be bound to any parameters for the command either because the command does not take pipeline input or the input and its properties do not match any of the parameters that take pipeline input.] to param [InputObject] SUCCESSFUL
DEBUG: ParameterBinding Information: 0 : MANDATORY PARAMETER CHECK on cmdlet [format-default]
DEBUG: ParameterBinding Information: 0 : BIND NAMED cmd line args [Set-StrictMode]
DEBUG: ParameterBinding Information: 0 :     BIND arg [1] to parameter [Version]
DEBUG: ParameterBinding Information: 0 :         Executing DATA GENERATION metadata: [Microsoft.PowerShell.Commands.SetStrictModeCommand+ArgumentToVersionTransformationAttribute]
DEBUG: ParameterBinding Information: 0 :             result returned from DATA GENERATION: 1.0
DEBUG: ParameterBinding Information: 0 :         COERCE arg to [System.Version]
DEBUG: ParameterBinding Information: 0 :             Parameter and arg types the same, no coercion is needed.
DEBUG: ParameterBinding Information: 0 :         Executing VALIDATION metadata: [Microsoft.PowerShell.Commands.SetStrictModeCommand+ValidateVersionAttribute]
DEBUG: ParameterBinding Information: 0 :         BIND arg [1.0] to param [Version] SUCCESSFUL
DEBUG: ParameterBinding Information: 0 : BIND POSITIONAL cmd line args [Set-StrictMode]
DEBUG: ParameterBinding Information: 0 : MANDATORY PARAMETER CHECK on cmdlet [Set-StrictMode]
DEBUG: ParameterBinding Information: 0 : CALLING BeginProcessing
DEBUG: ParameterBinding Information: 0 : CALLING EndProcessing
DEBUG: ParameterBinding Information: 0 : BIND NAMED cmd line args [Set-StrictMode]
DEBUG: ParameterBinding Information: 0 :     BIND arg [1] to parameter [Version]
DEBUG: ParameterBinding Information: 0 :         Executing DATA GENERATION metadata: [Microsoft.PowerShell.Commands.SetStrictModeCommand+ArgumentToVersionTransformationAttribute]
DEBUG: ParameterBinding Information: 0 :             result returned from DATA GENERATION: 1.0
DEBUG: ParameterBinding Information: 0 :         COERCE arg to [System.Version]
DEBUG: ParameterBinding Information: 0 :             Parameter and arg types the same, no coercion is needed.
DEBUG: ParameterBinding Information: 0 :         Executing VALIDATION metadata: [Microsoft.PowerShell.Commands.SetStrictModeCommand+ValidateVersionAttribute]
DEBUG: ParameterBinding Information: 0 :         BIND arg [1.0] to param [Version] SUCCESSFUL
DEBUG: ParameterBinding Information: 0 : BIND POSITIONAL cmd line args [Set-StrictMode]
DEBUG: ParameterBinding Information: 0 : MANDATORY PARAMETER CHECK on cmdlet [Set-StrictMode]
DEBUG: ParameterBinding Information: 0 : CALLING BeginProcessing
DEBUG: ParameterBinding Information: 0 : CALLING EndProcessing
DEBUG: ParameterBinding Information: 0 : BIND NAMED cmd line args [Set-StrictMode]
DEBUG: ParameterBinding Information: 0 :     BIND arg [1] to parameter [Version]
DEBUG: ParameterBinding Information: 0 :         Executing DATA GENERATION metadata: [Microsoft.PowerShell.Commands.SetStrictModeCommand+ArgumentToVersionTransformationAttribute]
DEBUG: ParameterBinding Information: 0 :             result returned from DATA GENERATION: 1.0
DEBUG: ParameterBinding Information: 0 :         COERCE arg to [System.Version]
DEBUG: ParameterBinding Information: 0 :             Parameter and arg types the same, no coercion is needed.
DEBUG: ParameterBinding Information: 0 :         Executing VALIDATION metadata: [Microsoft.PowerShell.Commands.SetStrictModeCommand+ValidateVersionAttribute]
DEBUG: ParameterBinding Information: 0 :         BIND arg [1.0] to param [Version] SUCCESSFUL
DEBUG: ParameterBinding Information: 0 : BIND POSITIONAL cmd line args [Set-StrictMode]
DEBUG: ParameterBinding Information: 0 : MANDATORY PARAMETER CHECK on cmdlet [Set-StrictMode]
DEBUG: ParameterBinding Information: 0 : CALLING BeginProcessing
DEBUG: ParameterBinding Information: 0 : CALLING EndProcessing
DEBUG: ParameterBinding Information: 0 : BIND NAMED cmd line args [Set-StrictMode]
DEBUG: ParameterBinding Information: 0 :     BIND arg [1] to parameter [Version]
DEBUG: ParameterBinding Information: 0 :         Executing DATA GENERATION metadata: [Microsoft.PowerShell.Commands.SetStrictModeCommand+ArgumentToVersionTransformationAttribute]
DEBUG: ParameterBinding Information: 0 :             result returned from DATA GENERATION: 1.0
DEBUG: ParameterBinding Information: 0 :         COERCE arg to [System.Version]
DEBUG: ParameterBinding Information: 0 :             Parameter and arg types the same, no coercion is needed.
DEBUG: ParameterBinding Information: 0 :         Executing VALIDATION metadata: [Microsoft.PowerShell.Commands.SetStrictModeCommand+ValidateVersionAttribute]
DEBUG: ParameterBinding Information: 0 :         BIND arg [1.0] to param [Version] SUCCESSFUL
DEBUG: ParameterBinding Information: 0 : BIND POSITIONAL cmd line args [Set-StrictMode]
DEBUG: ParameterBinding Information: 0 : MANDATORY PARAMETER CHECK on cmdlet [Set-StrictMode]
DEBUG: ParameterBinding Information: 0 : CALLING BeginProcessing
DEBUG: ParameterBinding Information: 0 : CALLING EndProcessing
Get-WmiObject : The input object cannot be bound to any parameters for the command either because the command does not take pipeline input or the input and its properties do not match any of the parameters that take pipeline input. At line:1 char:96
+ ... puters.csv  |  Get-WmiObject -Class win32_bios}
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{Computername=localhost}:PSObject) [Get-WmiObject], ParameterBindingException
    + FullyQualifiedErrorId : InputObjectNotBound,Microsoft.PowerShell.Commands.GetWmiObjectCommand
 
DEBUG: ParameterBinding Information: 0 : CALLING EndProcessing
DEBUG: ParameterBinding Information: 0 : CALLING EndProcessing


SMBIOSBIOSVersion : R1120Y6
Manufacturer      : American Megatrends Inc.
Name              : BIOS Date: 09/23/09 11:58:43 Ver: 08.00.10
SerialNumber      : 27524969-5001935
Version           : Sony - 20101011
#################################################################################################################################################