---
title: Using nativefier for creation of desktop application based on electron with any website
date: 2018-02-26T16:02:36+00:00
authors:
- admin

categories:
  - ALL
---
![magnolia cms desktop](posts/magnolia-cms-desktop.png "")
Recently I was working a bit more with [Magnolia CMS](https://www.magnolia-cms.com/). While I was working on it locally I really wanted to basically just package it as desktop application so it would be a bit easier to use it locally or even having multiple sources pointing in its own direction.

So let&#8217;s take a good look first for a hosted page in my case I pointed it to Magnolia on a server and secondly let&#8217;s try it on localhost as well.

&nbsp;

1.) We will need [Nativefier](https://github.com/jiahaog/nativefier)

2.) Install nativefier with npm:

`npm install nativefier -g`

3.) Use it on any website you need(in my case it was a special URL, so i just put it here as a placeholder):

`nativefier www.website.com`

4.) It will create an application in whichever directory you are positioned in the terminal. Navigate to it.

5.) Open your application and enjoy your beautiful little desktop app!

&nbsp;

References:

Nativefier(used for transformation): <https://github.com/jiahaog/nativefier>

Electron(It&#8217;s used for actual app): <https://electronjs.org/>

npm(used for installing Nativefier): <https://www.npmjs.com/>