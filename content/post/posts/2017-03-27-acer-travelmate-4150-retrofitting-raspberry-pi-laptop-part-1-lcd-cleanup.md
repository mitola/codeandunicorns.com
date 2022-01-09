---
title: 'Acer Travelmate 4150 retrofitting to Raspberry Pi Laptop: Part 1 - LCD and cleanup'
date: 2017-03-27T14:00:55+00:00
authors:
- admin

categories:
  - ALL
  - Electronics
  - How to
tags:
  - Acer
  - electronics
  - Laptop
  - LCD
  - LVDS
  - raspberry pi 3
---
![acer-travelmate-laptop](posts/acer-travelmate-laptop.jpg "")

**First about the idea:** 

I decided to try to create my own laptop with using one of older laptops not useful for anything else. Idea was to find a cheap one on ebay with working LCD and keyboard, ignoring everything else.

**Choosen laptop:**  
Acer Travelmate 4150 15 Intel Centrino 1.73Ghz spares/rebuild. // Cost 15£

Perfect, It arrived fairly soon and everything seems to be in order. The laptop has only a working LCD and keyboard (possibly touchpad too), but motherboard is fried, which is absolutely fine in this case.

I started with disassembly until I removed power supply, DVD drive, fan, motherboard itself (wohoo, lots of spare parts).  
After that the laptop suddenly transformed into super light shell 😀 it was losing pounds by the hour.

This was the final result:

![acer-travelmate-laptop-dissasembled](posts/acer-travelmate-laptop-dissasembled.jpg "")

The first step was completed with this. The second one was figuring out which connector is used by the monitor, and for that I had to investigate quite a lot, and hopefully found the correct one as per the laptops original specification:  
Specification sheet for the LCD: <https://www.manualslib.com/manual/232721/Acer-Travelmate-4150.html?page=45#manual>

Now I have ordered hopefully proper driver and LCD converted for the associated LVDS Connector.

The driver board which seems to be compatible is B150XG01 V2 with controller is:  
[kit for LTN150XB-L03 TV+HDMI+VGA+US<wbr />B LCD LED screen Controller Driver Board](http://www.ebay.co.uk/itm/112217998323?euid=7f448b53378c4611aec61b3ed341e31e&bu=44446927681&cp=1&sojTags=bu=bu)

**Next steps:**  
- Figure out the most appropriate power supply. One of possibilities would be using Li-ion batteries to which i connect a proper charging circuit. We need to keep in mind that we need to supply power to Raspberry pi 3 (maybe Zero W? , not sure yet) and to LCD converter. Both of them at 12V & 2A, hopefully we can make the battery actually last, or play with different configs without killing the hardware.
- Make a final connection with LVDS circuit,LCD circuit and the 15&#8243; LCD screen itself.
- Check how to connect the specific keyboard, or if it is more feasible to chisel laptop a bit more and inserting a custom keyboard is more efficient and logical. because yeah, different keyboards in laptops can act quite differently, sometimes too much manual work is involved.
