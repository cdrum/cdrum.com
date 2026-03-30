---
title: 'ThermostatPi - Controlling airconditioning with the Raspberry Pi and USB IR Toy - First Release!'
date: Sat, 02 Mar 2013 09:44:00 +0000
draft: false
tags: ['hacks', 'irtoy', 'projects', 'raspberrypi', 'ThermostatPi']
---


Finally got around to getting the first release of my current project up!  
  
**Code:** [https://bitbucket.org/cdrum/thermostatpi](https://bitbucket.org/cdrum/thermostatpi)  
  
It's called **ThermostatPi** and it makes use of a [Raspberry Pi](http://www.raspberrypi.org/), a [USB IR Toy 2](http://dangerousprototypes.com/docs/USB_Infrared_Toy) (see my library [here](http://www.cdrum.com/2013/02/php-library-for-receiving.html)), your bargain basement [DS18B20 One Wire Temperature Sensor](https://www.google.com/search?q=ds18b20), some Blu-Tack, and a few other miscellaneous wires and such.  
  
In it's current form, it monitors temperature, and allows the user through a web browser control the wall mount airconditioner over IR. The code contains a command line tool to store presets. (In my case, my air conditioner is complicated, and I couldn't extract individual button clicks, but instead, the complete configuration of whatever is on the remote at the time.  
  

[![](/uploads/IMG_20130302_172355-225x300.jpg)](/uploads/IMG_20130302_172355.jpg)

  

  

Software
--------

I wrote everything in PHP. I used [Foundation](http://foundation.zurb.com/) for the web framework. I used RRDTool for charting.

  

Right now, there are two major screens. First is the current temperature with historic graph.

  

[![](/uploads/2013-03-02_17-13-24-272x300.jpg)](/uploads/2013-03-02_17-13-24.jpg)

The next tab is there the controls are shown.

  

[![](/uploads/2013-03-02_17-15-47-300x198.jpg)](/uploads/2013-03-02_17-15-47.jpg)

  
 You see to the right are the last 6 or so commands run. I use SQLite3 to store the history. (I plan to add settings to the db as well down the road).  
  
Using Foundation's alerts, I show immediate feedback after button presses.  
  

![](/uploads/2013-03-02_17-16-28-300x181.jpg)

It also works on Mobile, thanks to Foundation.

  

[![](http://2.bp.blogspot.com/-juKGf1IQpKE/UTHHf5HaPjI/AAAAAAAAW74/gRgtOCZjHBQ/s320/Screenshot_2013-03-02-17-17-16.png)](http://2.bp.blogspot.com/-juKGf1IQpKE/UTHHf5HaPjI/AAAAAAAAW74/gRgtOCZjHBQ/s1600/Screenshot_2013-03-02-17-17-16.png)

  
  

I have a cron running to update the history charts each minute.

  

Lastly, I use dyndns so I can access it from my mobile phone. Makes it simple to turn on the air con when I'm on the way home!

  

Hardware
--------

This is pretty self explanatory. I have the DS18B20 chip plugged into the GPIO pins.

  

[![](/uploads/IMG_20130219_203321-300x225.jpg)](/uploads/IMG_20130219_203321.jpg)

  

And lastly, the IR Toy is positioned to point at the air con using Blu-Tack. Thank goodness for Blu-Tack!

  

[![](/uploads/IMG_20130219_203332-300x225.jpg)](/uploads/IMG_20130219_203332.jpg)

  

 Next Up
--------

I want to start building some intelligence into it. I want to set a temperature, and have it attempt to maintain that temperature. We'll see how that goes.