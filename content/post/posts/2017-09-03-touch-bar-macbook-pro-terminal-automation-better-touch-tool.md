---
title: 'Touch bar (Macbook pro) terminal automation & Better Touch Tool'
date: 2017-09-03T22:15:38+00:00
author: 
- admin

categories:
  - ALL
---
![overview_better_touch_tool-min](posts/overview_better_touch_tool-min.png "")
Recently I upgraded my work computer to MacBook Pro 13 &#8221; with the new touch bar. But to be totally honest I quite dislike the default customisability of the touch bar that is included in os X by default.

After a careful and in depth review of different solutions that could be use to amend that situation I stumbled upon BetterTouchTool (BTT in short), which is a great productivity enchanter in many areas such as hotkeys, gestures etc.

But personally, I am at least for the time being only interested in their TouchBar capabilities. On the picture above it's an example snapshot of couple of my current shortcuts/workflows. You can add buttons with images, just text or both. Or widgets as well such as for example a Spotify widget that nicely enhances the experience.

On the left side you can see several ways of touch bar config. For example Global ones will persist and application specific will only popup on the bar based on the application that you have opened. In my case for IntelliJ IDEA I use debug workflow to start up the lovely debugger.

But let's cut the chase and take a look at a specific use case.

&nbsp;

![overview_better_touch_tool-min](posts/HippoCms_shortcut-min.png "")

[Hippo CMS](https://www.onehippo.com/en) (Bloomreach experience now) is a Java based CMS System in short. and since I thought oh why not make the launching a bit simpler? So I created a relatively simple workflow with couple of steps to connect to Terminal app first.

After that as you can see in the image below I attached keyboard shortcut to go to a right tab in the Terminal application. I always have when working with this particular system, the process itself opened in first tab, therefore the shortcut will always take me from the outer most right tab which due to how i am used to terminal is always opened (if theres more than one)  to move back to the first tab.

![Sending_shortcut_to_terminal-min](posts/Sending_shortcut_to_terminal-min.png "")

Great, now we focused on correct tab, after that we stop running process of Hippo in case it still runs from before with a simple CTRL + C shortcut.

After that we input the text through typing (one of the options under Predefined Action) to make the rebuild. Command itself looks something like this:

`cd PATH_TO_PROJECT && mvn clean verify && mvn -P cargo.run`

Lovely, everything looks nice, we send ENTER button and it triggers a nice maven clean and build itself.

  <em>A small bit of automation to save seconds at a time, one by one. </em>