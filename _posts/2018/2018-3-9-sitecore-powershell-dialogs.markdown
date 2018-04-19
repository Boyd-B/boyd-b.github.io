---
layout: post
title:  "Sitecore Powershell Dialogs"
date:   2018-3-9 11:57
categories: Sitecore Powershell
image: https://picsum.photos/400/400/?rnd=0309
---

Have you tried to create a speak dialog for sitecore and failed? Or perhaps it took more time than you had available. Perhaps you just quit, sobbing in frustration. Why can't it just be easy? Oh, anyone who has successfully made one before may say it's not so bad once you get to know it, but that learning curve is like climbing a cliff. Without a safety line. If you fail, you're starting over from scratch.

## There is an easy way!

After struggling with creating a very simple dialog and endless attempts to find a new way to go about creating a dialog and achieving our goal, I finally stumbled onto a solution to replace those nasty sitecore speak dialogs using powershell. 

I always thought powershell was a pretty good extension, but I hadn't really used it. Not until I looked up creating an item with powershell and seeing a link for interactive dialogs. Then it clicked. I didn't have to use speak at all! The best part was creating a simple script and **it just worked**! Something speak can't say for itself.

### Ok, enough rambling here's how to make a powershell dialog.

First off, (Here's a link to the documentation)[https://sitecorepowershell.gitbooks.io/sitecore-powershell-extensions/interactive-dialogs.html].

Next, if you haven't installed powershell extensions. **DO SO!**

Now that you have my newest favorite module installed (taking over SXA's spot at the top) open your contend editor and head to master:/sitecore/system/Modules/PowerShell/Script Library

Insert a new PowerShell Script Module Folder to organize your scripts. You have two options for creating your scripts, manually and through PowerShell ISE window. (I'll explain adding items manually but you can skip to the ISE since you'll need to go there anyway for it to generate buttons from your scripts)

### Add items Manually

From this new folder, insert a new module. You will see a host of options for you to choose from. Start with Content Editor Ribbon and the script will create skeleton items matching your ribbon bar.

Pick a ribbon tab to place a new script into like Develop. Then add a new script.

### Powershell ISE

Open your sitecore launchpad and select PowerShell ISE. Or if you in the desktop, you can find it in Development Tools > PowerShell ISE.

*Welcome to your new favorite screen.* If it isn't already, it will be.

At this point you can run some simple dialogs, such as showing an alert.
{% highlight powershell %}
Show-Alert -Title "SPE is great!"
{% endhighlight %}

But let's jump into something more useful. For that, you need to create a property object to define your modal. Here's a basic example you can find in the documentation. I am going to update it to include some minor changes. Namely checking so see if the user hit cancel and exiting. That's only *kind* of important.

{% highlight powershell %}
$options = @{
    "Option A"="a"
    "Option B"="b"
}

$props = @{
    Parameters = @(
        @{Name="selectedOption"; Title="Form input title"; Options=$options; Tooltip="Form input subtitle"}
    )
    Title = "Dialog Title"
    Description = "Dialog subtitle/description"
    Width = 300
    Height = 300
    ShowHints = $true
}
$userInput = Read-Variable @props
if($userInput -eq "cancel"){
    exit
}

Show-Input $selectedOption

{% endhighlight %}

Hit the play button and see your dialog. The value of your selection goes into a variable with the same name as you defined in the Parameters part of the props object. In this case, accessing $selectedOption will net you the options where as $userInput is the value of the button selected.

### Now let's make it useful

Now that we have a dialog, let's change the options to something useful by adding a sitecore query and setting the dropdown to some item ids.

{% highlight powershell %}

$options = @{}
$themes = Get-Item master: -Query "/sitecore/Media Library/Themes//*[@@templatename='Theme']"
$themes | ForEach-Object {
  Write-Host "Item name: " + $_.Name + $_.ID
  $options.Add($_.Name, $_.ID)
}

{% endhighlight %}

I originally created this as a theme selector for SXA so that's what you see in the query. Update the query to anything you desire at this point to get a list of items. Add the name and id to the options object and pass it along to the same dialog we created and you'll have a dropdown with friendly item names with the underlying sitecore id as the value.

Access the item with the following:
{% highlight powershell %}

$theme = Get-Item $selectedOption

{% endhighlight %}

Now you have the item the user has selected. Now it's time to do some actions. You can create items, delete items, move items, even get statistics. You can upload files, download files and edit file blobs. You can edit item fields and create reports. You can basically do anything you could with c# code, but right in the content editor itself.

## Summary

If you don't need a complicated dialog with fancy controls and just want a simple entry point with some user feedback, then using powershell interactive dialogs may be the right solution for you.

**Cheers!**

