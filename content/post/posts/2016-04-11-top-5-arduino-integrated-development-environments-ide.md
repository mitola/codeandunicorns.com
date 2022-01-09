---
title: Top 5 Arduino integrated development environments ( IDE )
date: 2016-04-11T17:23:08+00:00
authors:
- admin

categories:
  - ALL
  - How to
  - The Code
tags:
  - arduino
  - electronics
  - IDE
---
![Power_and_Pin13_LED_on_Arduino_Compatible_Board](posts/Power_and_Pin13_LED_on_Arduino_Compatible_Board.jpg "")

## [Original Arduino Software IDE](https://www.arduino.cc/en/Main/Software)

![Arduino](posts/Arduino1.6.4_IDE_small.png "")
<img class="aligncenter wp-image-1742" src="https://codeandunicorns.com/wp-content/uploads/2016/04/Arduino1.6.4_IDE_small.png" alt="Arduino1.6.4_IDE_small" width="593" height="522" srcset="https://codeandunicorns.com/wp-content/uploads/2016/04/Arduino1.6.4_IDE_small-300x264.png 300w, https://codeandunicorns.com/wp-content/uploads/2016/04/Arduino1.6.4_IDE_small.png 593w" sizes="(max-width: 593px) 100vw, 593px" /> 

Cost: Free

Description:  Default IDE's environment developed by Arduino. Great starting point for beginners and with perfect connector to physical boards and delpoyment of code. Most of Environments use at least a bit of API or connection of Arduino software IDE to deploy the code to boards themselves. I seriously suggest installing this IDE just in case as an obligatory part of your favourite software choice

## [Arduino for Visual Studio](http://www.visualmicro.com/) / [Visual micro](http://www.visualmicro.com/)

Cost: Free

Description: Short excerpt from their website and compatibility to their tools: "Visual Micro is an so called extension for Microsoft Visual Studio 2008-2015 and for Atmel Studio 6.1-6.2 that allows any Arduino project to be developed, compiled, and then uploaded to any Arduino board, while taking benefit of the powerful features of Visual Studio and Atmel Studio."

Both Visual Studio and Atmel have their own advantages, such as Visual understands C++ only, but Atmel recognizes microcontroller native language as well.  Both of them also have rich plugin support so if you like more Microsoft-y way of tools and development, this choice may be for you.

## [Programino IDE](http://programino.com/)

Cost: Free trial but after that 29 € for personal license.

Description: Based on how much I looked in it, it provides a proper full-fledged Arduino development experience, but i haven't played to much with it. I would certainly suggest a further investigation to it due to its (apprently good) debugging skills, Dot-Matrix LCD-Designer and an actually nice adition of easier connection beetwen web project as well due to support of Arduino/Genuino (preferred language), C, C++, C Header, HTML & HTML 5, JavaScript, CSS, Text-Files. And if you use your custom board you can also take a use of "Configure your own user board (Teensy, eHajo, Atmel, Pretzel board etc.) or use a external programmer."

**P.S. Windows only**

## [PlatformIO IDE](http://platformio.org/)

Cost: Free

Description: If anyone is using Atom from Github, then it knows what's the starting point of this lovely IDE, as stated on their main website they say: "It's built on top of GitHub's Atom "hackable" text editor.

But for IDE regarding supported Arduino they also have support for the Serial Port Monitor,code completion which are quite important tools to use.  
It is meant as a great IDE for IoD (Internet of devices) and is not exclusive to Arduino devices. If you want to see full list of supported devices take a look at this list: <http://platformio.org/boards> .

Since playing a bit with it, I decided to give this one a lengthy trial to be my replacement of default Arduino Software if this helps with your decision.

## [Deviot](https://github.com/gepd/Deviot)

Cost: Free

Description: Not a real standalone editor, instead it is plugin for Sublime which many developers use, which is why I am adding it to the list since it can be easily included into your existing workflow. It's open source and available via github. When you add the plugin it will be available under it's own top tab option called [Deviot](https://github.com/gepd/Deviot) where you can define the board, port and all other settings.
