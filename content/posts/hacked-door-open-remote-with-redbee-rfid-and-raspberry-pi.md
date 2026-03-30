---
title: 'Hacked Door Open Remote with RedBee RFID and Raspberry Pi'
date: Tue, 29 Jan 2013 13:00:00 +0000
draft: false
tags: ['gadgets', 'geek', 'hacks', 'raspberrypi', 'redbee', 'rfid', 'xbee']
---


On 2 Fridays ago, we had a "hackathon" at work where we stayed back and worked on personal hacks/projects. The entry door to our office is controlled by a 4-number code. We have a remote control to open (unlock) it when someone rings the bell. We had a bunch of RFID equipment sitting around for the past year and a half, and I really wanted to make use of it.  
  
  

**[RedBee RFID Reader](http://www.roboticsconnection.com/p-99-redbee-rfid-reader-v11.aspx)**

We got a few of these back in 2011 when we were looking at options for an RFID solution. We chose this because we liked the openness of the hardware APIs, however, we ultimately chose a different solution (the [pcProx USB reader](http://www.rfideas.com/products/pcprox_readers/pcprox_enroll/index.php)).

[![](/uploads/RFIDReaderLarge-300x252.png)](/uploads/RFIDReaderLarge.png)

(photo from [http://www.roboticsconnection.com/p-99-redbee-rfid-reader-v11.aspx](http://www.roboticsconnection.com/p-99-redbee-rfid-reader-v11.aspx))

  

[Digi XBee Wireless 802.15.4 Wireless Module](http://www.digi.com/products/wireless-wired-embedded-solutions/zigbee-rf-modules/point-multipoint-rfmodules/xbee-series1-module#overview)

We purchased a bunch of these as they fit directly in the RedBee RFID reader - making it a good solution for deploying RFID at on-ground activations. Again, we ultimately chose a different technology, but this wireless module (2 of them actually) would make this hack even better.

[![](/uploads/is.jpeg)](/uploads/is.jpeg)

(photo from [http://www.trossenrobotics.com/store/p/6320-RedBee-Wireless-Upgrade-Kit.aspx](http://www.trossenrobotics.com/store/p/6320-RedBee-Wireless-Upgrade-Kit.aspx))

  
**[Sparkfun XBee Explorer USB](https://www.sparkfun.com/products/8687)**  

This plugged into a computer, with the XBee module plugged in allows the "base station" to communicate with multiple devices through the plug-and-play mesh network created hassle free with the XBee modules.

[![](/uploads/08687-01b-300x300.jpg)](/uploads/08687-01b.jpg)

(photo from [https://www.sparkfun.com/products/8687](https://www.sparkfun.com/products/8687))

  
Finally, we needed another door remote to hack.  

[![](/uploads/IMG_20130118_180151-300x225.jpg)](/uploads/IMG_20130118_180151.jpg)

  

We figured we could hack this and mimic a button press with one of the Raspberry Pi's GPIO pins by bypassing the button, controlled by the GPIO pin. I did my homework earlier in the week and found that I needed a solid state relay to bypass the button pressing. I found one at Element 14 (my new favorite online store).

  

**[Crydom CN048D05 - SSR, SIP, 48VDC/0.1A, 5VDC INPUT](http://sg.element14.com/jsp/search/productdetail.jsp?SKU=1821824)**

[![](/uploads/1821824-40.jpg)](/uploads/1821824-40.jpg)

This I figured would work just nice. The GPIO pins of the Raspberry Pi are powered by 3.3V, and the Door remote control is powered by a 12V battery (whether or not that's the voltage that gets sent through the push-to-make buttons or not, no idea) - this SSR allows for a 0V to 48VDC range on the load pins, and a 3V to 12V on the control pins - perfect for this project.

  

I worked on this with my colleague Yasin. I would take care of the remote hacking and the Raspberry Pi set up - he would take care of the RFID Reader interface and simple DB/logic coding.

  

**The Equipment**

[![](/uploads/IMG_20130118_180442-300x225.jpg)](/uploads/IMG_20130118_180442.jpg)

  

**Off To Work**

I found the correct pins to use for my soldering. 

  

[![](/uploads/IMG_20130118_181612-300x225.jpg)](/uploads/IMG_20130118_181612.jpg)

  
Soldered the ends to the control pins of the SSR, and then added wires to plug into the Raspberry pi.  
  

[![](/uploads/IMG_20130118_191137-300x225.jpg)](/uploads/IMG_20130118_191137.jpg)

  

And here it is. Simple.

  

[![](/uploads/IMG_20130118_201359-300x225.jpg)](/uploads/IMG_20130118_201359.jpg)

  

Yes, you can order Domino's using Midori on the Raspberry Pi, as I did for much needed fuel. On top of that window, you can see the terminal testing out the RFID scanning. We were lucky to note that we only needed to monitor a device (by cating in the simplest form) for the scans.  
  

[![](/uploads/IMG_20130118_201741-300x225.jpg)](/uploads/IMG_20130118_201741.jpg)

  

My colleague Yasin handled the coding on the Raspberry Pi, using Python and SQLite to manage the RFID device monitoring and scan checking. We managed to get a prototype working only after about two hours or so. Here are some videos. Bare with me, had a few beers.

  

[http://video.google.com/googleplayer.swf?videoUrl=https://redirector.googlevideo.com/videoplayback?requiressl%3Dyes%26id%3Db740b4e39cac8cf5%26itag%3D5%26source%3Dpicasa%26cmo%3Dsecure\_transport%253Dyes%26cmo%3Dsensitive\_content%253Dyes%26ip%3D0.0.0.0%26ipbits%3D0%26expire%3D1495582565%26sparams%3Drequiressl,id,itag,source,ip,ipbits,expire%26signature%3DFB8BF7DF1BE26801D555B32DAF2CA671D7AA4C1.510CFC0EDA8CA7A86146667280A11907167D5B25%26key%3Dck2](http://video.google.com/googleplayer.swf?videoUrl=https://redirector.googlevideo.com/videoplayback?requiressl%3Dyes%26id%3Db740b4e39cac8cf5%26itag%3D5%26source%3Dpicasa%26cmo%3Dsecure_transport%253Dyes%26cmo%3Dsensitive_content%253Dyes%26ip%3D0.0.0.0%26ipbits%3D0%26expire%3D1495582565%26sparams%3Drequiressl,id,itag,source,ip,ipbits,expire%26signature%3DFB8BF7DF1BE26801D555B32DAF2CA671D7AA4C1.510CFC0EDA8CA7A86146667280A11907167D5B25%26key%3Dck2)

  

[http://video.google.com/googleplayer.swf?videoUrl=https://redirector.googlevideo.com/videoplayback?requiressl%3Dyes%26id%3D4c43660a737ef83c%26itag%3D5%26source%3Dpicasa%26cmo%3Dsecure\_transport%253Dyes%26cmo%3Dsensitive\_content%253Dyes%26ip%3D0.0.0.0%26ipbits%3D0%26expire%3D1495582565%26sparams%3Drequiressl,id,itag,source,ip,ipbits,expire%26signature%3DB5C6994F9D11F4561B7B37F602117D90D424D42B.6F25FA33C0ABE9B838ED7A1CDEE8538365572061%26key%3Dck2](http://video.google.com/googleplayer.swf?videoUrl=https://redirector.googlevideo.com/videoplayback?requiressl%3Dyes%26id%3D4c43660a737ef83c%26itag%3D5%26source%3Dpicasa%26cmo%3Dsecure_transport%253Dyes%26cmo%3Dsensitive_content%253Dyes%26ip%3D0.0.0.0%26ipbits%3D0%26expire%3D1495582565%26sparams%3Drequiressl,id,itag,source,ip,ipbits,expire%26signature%3DB5C6994F9D11F4561B7B37F602117D90D424D42B.6F25FA33C0ABE9B838ED7A1CDEE8538365572061%26key%3Dck2)

  

  
[http://video.google.com/googleplayer.swf?videoUrl=https://redirector.googlevideo.com/videoplayback?requiressl%3Dyes%26id%3D12a77c7936dfb0cb%26itag%3D5%26source%3Dpicasa%26cmo%3Dsecure\_transport%253Dyes%26cmo%3Dsensitive\_content%253Dyes%26ip%3D0.0.0.0%26ipbits%3D0%26expire%3D1495582565%26sparams%3Drequiressl,id,itag,source,ip,ipbits,expire%26signature%3D3E315C5A5C8DA84C2280E53A2AE1241FEDADF97D.A6E98B4DF24CA4C9F6148D106B8B3B0E2A1C7B04%26key%3Dck2](http://video.google.com/googleplayer.swf?videoUrl=https://redirector.googlevideo.com/videoplayback?requiressl%3Dyes%26id%3D12a77c7936dfb0cb%26itag%3D5%26source%3Dpicasa%26cmo%3Dsecure_transport%253Dyes%26cmo%3Dsensitive_content%253Dyes%26ip%3D0.0.0.0%26ipbits%3D0%26expire%3D1495582565%26sparams%3Drequiressl,id,itag,source,ip,ipbits,expire%26signature%3D3E315C5A5C8DA84C2280E53A2AE1241FEDADF97D.A6E98B4DF24CA4C9F6148D106B8B3B0E2A1C7B04%26key%3Dck2)