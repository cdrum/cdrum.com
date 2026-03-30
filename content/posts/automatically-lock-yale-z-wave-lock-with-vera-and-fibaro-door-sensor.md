---
title: 'Automatically lock Yale Z-Wave Lock with Vera and Fibaro Door Sensor'
date: Sun, 02 Nov 2014 12:24:00 +0000
draft: false
tags: ['home automation', 'sensors', 'vera', 'yale']
---


I got another toy today - the Fibaro Door and Window Sensor FGK-107.  
  

[![](/uploads/11111.png)](/uploads/11111.png)

  
The primary reason why I got it was I wanted to be able to automatically lock my door when it is closed. The Yale lock I got 2 weeks ago is Z-Wave enabled, which means I can connect it (wirelessly) to my Vera Lite home automation controller. One of the shortcomings of this lock is it won't automatically lock when (and only when) the door is closed (like other fancy digital locks on the market these days). But no matter, the fact that it is Z-Wave enabled is it's strength.  
  
I purchased the Fibaro Z-Wave Door and Window sensor (FGK-107) today for the primary purpose of being able to tell when the door is closed (or not). The idea is, program a trigger so when the door is closed, send a signal to the lock to lock itself.  
  
Connecting the sensor to the Vera was pretty straight forward, although the process is a bit different than what the instructions say. Take a look at this URL: http://www.dreamgreenhouse.com/reviews/2014/fibaroFGK101/index.php  
  
Once it's all configured, it looks something like this in Vera:  
  
  

[![](/uploads/2014-11-02_17-25-44.png)](/uploads/2014-11-02_17-25-44.png)

  

Now, on to creating the trigger. From the Automation screen,

  

[![](/uploads/2014-11-02_20-20-11.png)](/uploads/2014-11-02_20-20-11.png)

  

Click on **New Scene**, then the Tr**iggers** sub tab. From there click "Add Trigger" and start configuring it. See my settings below:

  

  

[](/uploads/2014-11-02_17-25-44.png)[![](/uploads/2014-11-02_17-26-53.png)](/uploads/2014-11-02_17-26-53.png)

  
A few important steps:  
  

1.  Select the sensor device we just created
2.  Select "A sensor is tripped" for the type of event
3.  Select "not tripped" whcih is when the door is closed. When the door opens, the sensor **IS** tripped. So keep that in mind.

That's it. Save your settings, reload, and give it a go. Here it is in action: