---
title: Pibakery example of first setup with wifi and on boot VNC ( for updated version of pibakery)
date: 2017-02-25T13:48:07+00:00
author:
- admin

categories:
  - How to
  - programming
---
![pibakery-example-min](posts/pibakery-example-min.png "")

Recently Pibakery has released an update with updated blocks, that changed quite a bit in comparison to previous versions and improved with several additions of the new building blocks or simplification of the previous ones. Now you even have cron job scheduler which is absolutely amazing!

### First boot:

Let's start with taking a look at First boot part of the image above:

We start with enabling VLC at Boot. This is the only block i am not 100% if it needs to be done every time or only one time. But anyway, this will allow you to connect via your PC/OSx VLC client. I use a [VNC viewer ](https://www.realvnc.com/download/viewer/) for it, but there are many choices to choose from. When later on you connect your Raspberry pi to the network and find the IP, you should be able to connect to it by entering the ip with :0 or :5900 port at the end.

**Example:**

`192.1.0.245:0 or 192.1.0.245:5900`

The next option I have added is setting boot option block to a 'Desktop logged in', which will prove useful when using VNC, so you will be able to see the desktop without logging in or playing around the ssh.

After that I added another part of the recipe to the Pibakery template with setting the password of the future Rpi image, so you can make it at least more secure then having the default pi/raspberry combination.

After that I have added Wifi setup block with SSID and and password to automatically connect to it. And since I use Raspberry pi 3, it works beautifully out of the box.

&nbsp;

### On every boot:

As the last thing to the Pibakery I added VNC server at boot for now.

But in the future I would like to expand it with several additions, such as setting a cron job to automatically backup a remote server with duplicity.

&nbsp;