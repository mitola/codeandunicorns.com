---
id: 63
title: Setting up a home torrent/data server using an old computer
date: 2013-07-18T23:30:05+00:00
author: Matjaz Trcek
layout: post

image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com/photos/CpkOjOcXdUY)'
  focal_point: ""
  placement: 2
  preview_only: false

categories:
  - How to
tags:
  - ftp
  - rtorrent
  - server
  - ssh
  - startup
  - torrent
  - Ubuntu
---
We all have an old computer at home, that hasn’t been used in years, but because it still works, we hadn’t had the heart to trow it away. Well its time of misery has come to an end, we are going to convert it to a home data server. Using a torrent client to download files from the web, and running a ftp server on the machine, we will make your personal PC’s hard drive love you more, by not filling it with hundreds of gigabytes of data (read: movies, music), and making it available to any PC on your home network.

But why use Ubuntu server? I hear you ask, well I assume you are going to use an old computer and the server edition doesn&#8217;t use nearly as much resources as the desktop one or the Windows os, you can install Ubuntu desktop if you have a more powerful computer and follow the steps using the console.

<!--more-->

### Things you will need:

Computer: any old computer will do, but it has to have at least 128 Mb ram, 300 MHz processor, a graphics card, Lan/Wlan capability, installed Ubuntu 12.04 Server and a USB port (optional).

You will also need: monitor, keyboard, an additional external or internal HDD (>100Gb).

This guide assumes you have already installed Ubuntu server on your computer, for help installing click <a title="help" href="https://help.ubuntu.com/" target="_blank">here</a>!

### **Table of contents:**

  1. The first steps (installing the software)
  2. Configuring the software
  3. Last few steps
  4. Conclusion
  5. Troubleshooting

  1. ### **The first steps**

Once the system installs log in with your username and password and follow the steps.

<p style="padding-left: 30px;">
  <strong>1.1 SSH server</strong>
</p>

First you will need to install a SSH server, if you have selected the packages when installing Ubuntu you can skip to the next step.

We need the SSH server to remotely access our server.

> Install the SSH server using the comand:  
> _sudo apt-get install openssh-server_
> 
> Hit &#8220;y&#8221; and enter when asked if you want to install
> 
> Next we have to make sure the SSH server is running. Go to one of your other PC&#8217;s connected to the same  network the server is connected to and download PuTTY from [here](http://the.earth.li/~sgtatham/putty/latest/x86/putty.exe "putty"). Before we can connect we must get our server&#8217;s IP adress.
> 
> This can be done with the next command:  
> &#8220;_sudo ifconfig -a&#8221;_
> 
> This command will print out the list of the connected ethernet cards and their IPs, the IP will be written after &#8220;inet addr:&#8221;
> 
> Start PuTTY and in the field &#8220;host name (or IP address)&#8221; write the servers IP address and press &#8220;Open&#8221;. If you get a console asking you to login everything is working fine. From now on we can do everything from our remote computer, there is no need to jump back and forward from computer to computer. If you can not access the server, check the IP address and/or restart the server using the next command:
> 
> _&#8220;sudo reboot&#8221;_

<p style="padding-left: 30px;">
  <strong>1.2 FTP server</strong>
</p>

Next we will install the FTP server. This will allow us to get files to and from our server.

> Install the FTP server using this command:  
> &#8220;_sudo apt-get install pure-ftpd&#8221;_
> 
> To test if it works try:
> 
> _&#8220;sudo pure-ftpd&&#8221;_
> 
> If you get something like &#8220;Unable to start a standalone server: address already in use&#8221;, it is working.

**1.3 Torrent client**

The torrent client we will be using is Rtorrent

> As before we will be using apt-get to install it. Type the following command:
> 
> &#8220;sudo apt-get install rtorrent&#8221;
> 
> This should install Rtorrent.

**1.4 Mounting your external drive**

You will have to mount your external hard drive before you can continue, mounting means not only to connect it to the server, but instructing ubuntu to connect to it, so it can read/write to it.

> 1. Connect the drive to your server, I would recommend formatting the drive to ntfs file system and saving a .txt on it, this will make it easy to check if it is connected to our server.
> 
> 2. Wait a few seconds and input the following command: _&#8220;sudo fdisk -l&#8221;_
> 
> 3. It will output all of the disks connected to the server, we are looking for _/dev/sdb_ (WARNING the drive can have a different letter at the end, so it may be listed as /dev/sdc ), it should be the same size as your external disk. You will be using the partition on said disk which should be listed as /dev/sdb1 , if there are multiple partitions on the drive, they will have different numbers.
> 
> 4. Next we will make a directory where your external hard drive will be mounted to, I usually put it in&#8221; _/mnt&#8221;_ and call it _disk_. Type the following command: &#8220;_sudo mkdir /mnt/disk_ &#8220;. and check if it is there by typing: &#8220;_cd /mnt&#8221;_ this will take us to the /mnt directory, next type &#8220;_ls&#8221;_ to see the folder &#8220;disk&#8221;.
> 
> 5. To mount the disk, you must input the following command &#8220;_sudo mount /dev/sdb1 /mnt/disk&#8221;_ . Where sdb1 is the partition not the entire disk, you will get an error if you try to mount the entire disk not the partition.
> 
> 6. To check if the disk mounted correctly, type: &#8220;_ls /mnt/disk&#8221;_ and you should see the file you wrote on it in step 1.
> 
> 7. To automatically mount the disk, we must do some more work, but the following steps are not necessary, you can use &#8220;_sudo mount /dev/sdb1 /mnt/disk&#8221;_ when the server is restarted and it will mount just fine (not following the steps below can cause issues for rtorrent as it will start without the required folders loaded). In the next steps I will show you how to make a startup script, which will automatically mount the disk.
> 
> Use the following command to open **rc.local** with a text editor: &#8220;_sudo nano /etc/rc.local&#8221;_ , under the colored text and above the _exit 0,_ write the following: &#8220;_sudo mount /dev/sdb1 /mnt/disk&#8221; _ press &#8220;ctrl+o&#8221; and hit enter, this will save the document, then press &#8220;ctrl+x&#8221; and you should return to the command line, next we have to make the script executable, to achieve this type: &#8220;_sudo chmod +x /etc/rc.local&#8221;, this will change the file&#8217;s permission to be executed_ . When you reboot your server, the disk should automatically mount. There are other ways to mount an external hard drive, this is the easiest, but can have issues when more than one disk is connected to the computer.
> 
> This should be everything regarding the installation of the software and hardware. next we will configure the software we just installed.

**2 Configuring the software**

**2.1** **ssh server configuration**

> The ssh server doesn&#8217;t need any additional configuration, although it is recommended that you set up some security measures. In other words it is good to have some kind of safety measure to deny unauthorized users to connect to your server. This is only needed if your server is accessible from the internet. This is done by use of a personal key.
> 
> The guide is available [here](https://help.ubuntu.com/community/SSH/OpenSSH/Keys)!

**2.2 Ftp server configuration**

> For the FTP server the same goes as for the ssh server, it is a good idea to limit access. Thus keeping our data safe.
> 
> **Guide will be added soon**

**2.3 Rtorrent configuration**

Here we will configure our torrent client, it will automatically download and sort the files into folders. The process is a bit lengthy but not as hard as it may seem on first glance.

**2.3.1 Creating the folders**

> You will need to create some folders, for Rtorrent to start doing its magic.
> 
> 1. You will need to create the following folders on your disk: `rtorrent_watch rtorrent_completed rtorrent_loading rtorrent_sessions rtorrent_temp .` First we will create a folder named torrent on our disk: &#8220;_mkdir /mnt/disk/torrent&#8221;_ , navigate to the folder using: &#8220;_cd /mnt/disk/torrent&#8221;_ . Then create the folders using: &#8220;_mkdir `rtorrent_watch rtorrent_completed rtorrent_loading rtorrent_sessions rtorrent_temp"`_ . The folders are now created, to check it type: _ls_ . If the folders are not there check if you navigated to the correct location and/or try making the folders using _sudo._

**2.3.2 The rtorrent configuration file**

> 1. Next we will create the rtorrent configuration file named .rtorrent.rc . Go to &#8220;/mnt/disk&#8221; using: _cd /mnt/disk_ and get the rtorrent.rc by typing: _wget http://libtorrent.rakshasa.no/export/1303/trunk/rtorrent/doc/rtorrent.rc_ . Or download it to your windows desktop from [here](wget  http://libtorrent.rakshasa.no/export/1303/trunk/rtorrent/doc/rtorrent.rc) and upload it to your server&#8217;s &#8220;_/mnt/disk&#8221;_ folder by using a ftp client like [filezilla](http://sourceforge.net/projects/filezilla/files/FileZilla_Client/3.6.0/FileZilla_3.6.0_win32-setup.exe/download?accel_key=57%3A1352834775%3Ahttp%253A//filezilla-project.org/download.php%253Ftype%253Dclient%3A9bd31d89%24a23c74ffd0744680c9c5c6a5c7d875af8aa3a1bc&click_id=fde9e73e-2dc7-11e2-97bb-0200ac1d1d8a&source=accel).
> 
> 2. Now we have to edit the configuration file by typing _sudo nano /mnt/disk/rtorrent.rc_ . For every entry you will change you have to remove the &#8220;#&#8221; at the beginning of the line.
> 
> 3 First we will change the default location of where the completed torrents will be saved. In the rtorrent.rc find the line that says &#8220;# Default directory to save the downloaded torrents.&#8221;. Below this line change the entry so it says &#8220;directory = /mnt/disk/torrent/rtorrent_completed&#8221;.
> 
> 4 Next we will configure the watch directory, in this directory we will later put our .torrent files. Rtorrent will automatically check for new torrents and start downloading them and when it finishes it will delete the .torrent files. Find the line that says something like &#8220;#schedule = watch\_directory,5&#8230;.&#8221; and change the end from &#8220;./watch/\*.torrent&#8221; to &#8220;/mnt/disk/torrent/rtorrent\_watch/\*.torrent&#8221;. Also remove the &#8220;#&#8221; from the line below this entry.
> 
> 5 You can also change the other parameters, but do it at your own risk. Nothing bad can happen, but it may not work as intended. For more information click <a href="http://fsk141.com/rtorrent-the-complete-guide/" target="_blank">here</a>.
> 
> 6 Next we have to copy the configuration file and make it hidden so that rtorrent will be able to load it, we have to copy it to the root of our user folder in my case it is &#8220;/home/tomasys&#8221; for you it will be like &#8220;/home/your\_username\_here&#8221;. We do this with the following command &#8220;sudo cp /mnt/disk/rtorrent.rc /home/tomasys/.rtorrent.rc&#8221;
> 
> 7 Now we will test rtorrent if everything works. Copy a .torrent file to &#8220;/mnt/disk/rtorrent_watch&#8221; and start rtorrent with the command &#8220;rtorrent&#8221;. Rtorrent should start to automatically download the torrent. If it doesn&#8217;t, retry from step &#8220;2&#8221; of this subset. To quit rtorrent press &#8220;ctrl-q&#8221;

**3 The last few steps**

**3.1 making rtorrent start when the machine starts**

> So now we will configure rtorrent to start with the machine. To achieve this we must again edit &#8220;rc.local&#8221;. Type &#8220;_sudo nano /etc/rc.local&#8221; and below the text we added a few steps before add another line and type &#8220;screen rtorrent -n -o import=/home/UESRNAME/.rtorrent.rc&#8221; (where USERNAME is your username) press &#8220;ctrl-o&#8221; hit enter and &#8220;ctrl-x&#8221;. Next we must make sure that we have the program named screen. In the console type &#8220;sudo apt-get install screen&#8221; when asked if you want to download it hit &#8220;y&#8221;, if it is already downloaded it will state that the package is already the newest version and you don&#8217;t have to do anything. Reboot your system by typing &#8220;sudo reboot&#8221;, when the machine reboots rtorrent should start automatically and continue downloading. If it fails quit it with &#8220;ctrl-q&#8221; and check rc.local in case you misspelled something._

**3.2 connecting via ssh**

> Now that everything is working you may want to connect to the server via ssh. To do this you will use putty available <a href="http://the.earth.li/~sgtatham/putty/latest/x86/putty.exe" target="_blank">here</a>. Run it, in the &#8220;Host name&#8221; field type the server&#8217;s ip address (adress can be found via the &#8220;ifconfig&#8221; command) and hit &#8220;open&#8221;. you will be presented with the login screen much like what you see on your server. Login with your username and password and type &#8220;sudo screen -d -r&#8221; this will open your rtorrent client. To close it press &#8220;ctrl-a-d&#8221;. By not pressing the &#8220;ctrl-q&#8221; key combination, rtorrent keeps running in the background. To get it back just type &#8220;sudo screen -d -r&#8221; and rtorrent will reopen.

**** **3.3 Were almost done**

> So we are almost done, reboot your server using &#8220;sudo reboot&#8221;. And access its bios (bios is usually accessed by pressing the &#8220;del&#8221; key during startup). In the bios under the tab &#8220;advanced bios options&#8221; or &#8220;advanced power options&#8221;, try to find the option &#8220;wake up after power off&#8221; or something along those lines. Enable it, this will ensure that the server will automatically start up if the electrical power were to fail.
> 
> Now you can unplug everything from your server except for the keyboard, as the bios may halt startup if the keyboard is not detected

**4 Conclusion**

> Now you have a powerful torrent munching machine that you can put anywhere you like, it will automatically download and upload torrents for you. You can even configure it to start and stop at a specific time of the day, to seed torrents until a ratio is reached and much much more.

**5 Troubleshooting**

> If you followed all the steps to the letter you shouldn&#8217;t have any problems, but if you run into any problem first check back in the guide if you left something out or did something wrong. If all else fails leave a comment and i will try to answer it as soon as possible.