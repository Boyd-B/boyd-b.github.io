---
layout: post
title:  "Export and Import bacpac files with a simple powershell script"
date:   2018-3-1 02:56
categories: Powershell SQL
highlight: true
image: https://picsum.photos/400/400/?rnd=31
---
After trying various ways to copy a database from one server to another and failing 
with weird and unhelpful errors, I decided to brush off an old powershell script.

This script uses an array of database names and creates bacpac files. Note that you should check the filepath 
for the sqlpackage as you may have a different version installed. The transcript portion of the script mirrors 
the output of the script to a log file.

{% highlight ps1 linenos %}
$ErrorActionPreference="SilentlyContinue"
Stop-Transcript | out-null
$ErrorActionPreference = "Continue"
Start-Transcript -path $PSScriptRoot\MakeBacPac.log -append

"Starting Database Packaging at $(get-date)"

$server = ".\SQLSERVER2012"
$user = "sa"
$pass = "password"
$sqlpackage = "C:\Program Files (x86)\Microsoft SQL Server\130\DAC\bin\sqlpackage.exe"

$dbs = @(
    "dbName1",
    "dbName2"
)

if(![System.IO.File]::Exists($sqlpackage)){
    "SQL Package Not Found. Please Install."
	[System.Diagnostics.Process]::Start("https://msdn.microsoft.com/en-us/library/mt204009.aspx")
	exit
}
foreach ($dbname in $dbs) {
  
    $dbfile = "$PSScriptRoot\DB\" + $dbname + ".bacpac"
    
    $elapsed = [System.Diagnostics.Stopwatch]::StartNew()
    "Packaging " + $dbname + ": $(get-date) "
    & $sqlpackage /a:Export /ssn:$server /sdn:$dbname /su:$user /sp:$pass /tf:$dbfile
    "Finished " + $dbname + " @ $(get-date) Duration: $($elapsed.Elapsed.ToString())"
}
Stop-Transcript
{% endhighlight %}

### Deploy bacpacs to your server

To import the bacpacs, we simply change the server it's going to (db's can't already exist)
and change the sqlpackage command to the following line. Everything else remains the same.

{% highlight %}
& $sqlpackage /a:Import /tsn:$server /tdn:$dbname /tu:$user /tp:$pass /sf:$dbfile
{% endhighlight %}
