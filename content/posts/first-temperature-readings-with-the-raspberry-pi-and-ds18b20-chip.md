---
title: 'First Temperature Readings with the Raspberry Pi and DS18B20 Chip'
date: Mon, 14 Jan 2013 14:47:00 +0000
draft: false
tags: ['geek', 'projects', 'raspberrypi', 'ThermostatPi']
---


I got my Raspberry Pi reading temperatures with the DS18B20 chip.  
  

[![](/uploads/IMG_20130114_204619-300x225.jpg)](/uploads/IMG_20130114_204619.jpg)

  
  
I followed some of the guidance at the following two URLs (although I had to flip the chip as the instructions are a bit confusing - basically, look at the diagram on the 1st link below, and swap (pin 1 is 3, pin 3 is 1)):  
  

1.  [http://www.cl.cam.ac.uk/freshers/raspberrypi/tutorials/temperature/](http://www.cl.cam.ac.uk/freshers/raspberrypi/tutorials/temperature/)
2.  [http://webshed.org/wiki/RaspberryPI\_DS1820](http://webshed.org/wiki/RaspberryPI_DS1820)

  
**My wiring:**  

[![](/uploads/IMG_20130114_211657-300x225.jpg)](/uploads/IMG_20130114_211657.jpg)

  

Originally, according to the first URL above, I had the sensor chip the other way around, which caused it to heat way up seconds after turning on the Raspberry Pi. Thanks to the second link above which has an actual photo of his set up, I managed to get it going.

  

I refer to the pins on the DS18B20 chip as follows:

[![](/uploads/photo-287x300.jpg)](/uploads/photo.jpg)

1.  Blue jumper is plugged into pin 7 (BCM GPIO 4) on the Raspberry Pi, and pin 2 on the DS18B20 chip
2.  White jumper is plugged into pin 6, which is a ground pin on the Raspberry Pi, and pin 3 on the DS18B20 chip
3.  Yellow jumper is plugged into pin 1, which is powered at 3.3V, and pin 1 on the DB18B20 chip
4.  The 4.7k ohms resister is connected to chips' 1 and 2 of the DB18B20 chip

  

BCM? See [https://projects.drogon.net/raspberry-pi/wiringpi/pins/](https://projects.drogon.net/raspberry-pi/wiringpi/pins/)

  

**Kernel Modules**

> sudo modprobe w1-gpio  
> sudo modprobe w1-therm

  
**Cating the device**  
  
The device files are located in **/sys/bus/w1/devices/\*** where \* = the serial number of the chip. In my case, the serial number is 28-0000032837ea. The file in that folder (the folder name is the S/N) that we need to cat is w1\_slave:  
  

> $ cat /sys/bus/w1/devices/28-0000032837ea/w1\_slave  
> a7 01 4b 46 7f ff 09 10 e0 : crc=e0 YES  
> a7 01 4b 46 7f ff 09 10 e0 t=26437

The line we're interested is the 2nd, the value after the t=. Just pull this, divide by 1000, and we determine my room is a horribly hot 26.437 degrees Celsius!  
  
**Initial Code**  
The first link above has code written in Python. I am lame and prefer PHP, so I wrote the following:  
  

> <?php

> if (!defined("THERMOMETER\_SENSOR\_PATH")) define("THERMOMETER\_SENSOR\_PATH", "/sys/bus/w1/devices/**28-0000032837ea**/w1\_slave");

> // Open resource file for thermometer  
> $thermometer = fopen(THERMOMETER\_SENSOR\_PATH, "r"); 

> // Get the contents of the resource  
> $thermometerReadings = fread($thermometer, filesize(THERMOMETER\_SENSOR\_PATH)); 

> // Close resource file for thermometer  
> fclose($thermometer); 

> // We're only interested in the 2nd line, and the value after the t= on the 2nd line  
> preg\_match("/t=(.+)/", preg\_split("/n/", $thermometerReadings)\[1\], $matches);  
> $temperature = $matches\[1\] / 1000; 

> // Output the temperature  
> print $temperature; 

> ?>

  
  
And there we are! Now it's time to start plotting this info using RRD. More on that later.