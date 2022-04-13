---
title: Interesting command lines
date: 2018-01-16T14:18:22+00:00
author:
- admin

categories:
  - programming
---
![star_wars_console_command](posts/star_wars_console_command.png "")
**Capture video Macbook webcam with cpu accelerated**

Captures video from webcam and encodes it using the accelerated hardware provided by videotoolbox framework. It takes about 20% cpu in a i5 2015 macbook air.

`ffmpeg -f avfoundation -framerate 30 -video_size 1280x720 -pix_fmt uyvy422 -i "0" -c:v h264_videotoolbox -profile:v high -b:v 3M -color_range 1 /tmp/out.mp4`

**Command-line russian roulette**

This command-line is so beautiful you don&#8217;t even want to run it. One in six chance, for your pc to go bye bye.

`[ $[ $RANDOM % 6 ] = 0 ] && rm -rf --no-preserve-root / || echo "Click"`

**Command for clock wising PDF pages**

To rotate all PDF pages clockwise:

`pdftk in.pdf cat 1-endeast output out.pdf`

**ffmpeg facebook videos to live stream  
**  
ffmpeg mp4 to facebook live steam

`ffmpeg -re -y -i mm.mp4 -b:a 128k -vcodec libx264 -pix_fmt yuv420p -vf scale=640:480 -r 30 -g 60 -f flv "rtmp://rtmp-api.facebook.com:80/rtmp/xxxxxxxxxx"`

**Generate random password in Linux CLI**

Generate a random password quickly in CLI using openssl  
`<br />
openssl rand -base64 12`

**Download an entire website  
**  
-p parameter tells wget to include all files, including images.

-e robots=off you don&#8217;t want wget to obey by the robots.txt file

-U mozilla as your browsers identity.

&#8211;random-wait to let wget chose a random number of seconds to wait, avoid get into black list.

Other Useful wget Parameters:

&#8211;limit-rate=20k limits the rate at which it downloads files.

-b continues wget after logging out.

-o $HOME/wget_log.txt logs the output

`wget --random-wait -r -p -e robots=off -U mozilla http://www.example.com`

**For how to watch the whole SW movie via a command**

Watch Star Wars via telnet  
Use Ctrl-] to stop it.

`telnet towel.blinkenlights.nl`