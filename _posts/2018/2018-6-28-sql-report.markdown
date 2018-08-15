---
layout: post
title:  "Convert PascalCase and camelCase to regular case for display | C# Extension"
date:   2018-4-14 11:59
categories: csharp extension
image: https://picsum.photos/400/400/?rnd=414
---

Run an sql report directly from SPE, and place it into the table for export.

{% highlight c# %}
    $dataSource = "FSJMM400DB"
    $database = "JoyceMeyer"
    $sqlCommand = "exec dbo.GetSitecoreLanguageByCountryName 'US'"
    $sqlCommand = "SELECT TOP 1000 [CountryID],[Iso3166Alpha2],[Iso3166Alpha3],[Iso3166Numeric],[CountryName] FROM [JoyceMeyer].[dbo].[Country]"

    $connectionString = "Data Source=$dataSource; " +
            "Integrated Security=SSPI; " +
            "Initial Catalog=$database"

    $connection = new-object system.data.SqlClient.SQLConnection($connectionString)
    $command = new-object system.data.sqlclient.sqlcommand($sqlCommand,$connection)
    $connection.Open()

    $adapter = New-Object System.Data.sqlclient.sqlDataAdapter $command
    $dataset = New-Object System.Data.DataSet
    $adapter.Fill($dataSet) | Out-Null

    $connection.Close()
    $dataSet.Tables.Count
    $dataSet.Tables[0].Rows | Show-ListView -Property CountryID,Iso3166Alpha2,Iso3166Alpha3,Iso3166Numeric,CountryName
{% endhighlight %}

[Example](http://rextester.com/HBYB76114)

**Cheers!**
