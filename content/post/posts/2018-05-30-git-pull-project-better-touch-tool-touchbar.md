---
title: Git pull with better touch tool on Touchbar
date: 2018-09-27T13:44:43+00:00
authors:
- admin

categories:
    - programming
---
This is a short tutorial on how to setup Terminal command with Better touch tool in one of my use cases.
In my case I used Synchronous blocking terminal command that git pulls from the repo and at the same time remakes changes to one configuration file to prepare it for local development.

Get BTT (bettertouchtool) and install it
Under TouchBar add a Global TouchBar button.  (Of course you can do it for keyboard,mouse etc. but I’m focusing on TouchBar in this case)
Choose a predefined action called: “Execute terminal command: Synchronous, blocking”
Add the snippet at the end of this article in the popup window.
With the press of the button you should be able to do the pull of the project with one file getting discarded on purpose and updated with custom config
That’s fine but it’s always good to get a fuller picture and understanding what everything actually means.

Let’s cover it in more detail.

First we CD to the location of our git project.

```
cd /Users/location-to-repo/repo-location &&
```

This command will discard changes in the configuration file that I always want to make same change to it after pulling it from master.

```
git checkout Vagrantfile >>
```

with >> meaning it will create a new file or append to existing out the log of it into gitplatformupdate.txt file.

```
/Users/location-to-repo/auto-output/gitplatformupdate.txt &&
```
git pull command inside our repo to update it with the newest changes.

and save it to the same log file

```
/Users/location-to-repo/auto-output/gitplatformupdate.txt 
```

With this command we do a search of replace of specific configuration string so it is adapted to my local environment on my Vagrantfile, but of course it works on any kind of config file. In some cases the -i .bak is needed due to OS specifics. But if it helps in my case i’m using mac OS. The command will create backup file with .bak at the end, and override the original file with the changes.

```
sed -i .bak 's/string-replace-int/with-string-replace-int/g' /Users/location-to-repo/repo-location/Vagrantfile
```

Whole code:

```
cd /Users/location-to-repo/repo-location &&
git checkout Vagrantfile >>
/Users/location-to-repo/auto-output/gitplatformupdate.txt &&
git pull >> 
/Users/location-to-repo/auto-output/gitplatformupdate.txt &&
sed -i .bak 's/string-replace-int/with-string-replace-int/g' /Users/location-to-repo/repo-location/Vag
```