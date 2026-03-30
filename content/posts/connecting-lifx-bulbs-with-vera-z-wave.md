---
title: 'Connecting LIFX Bulbs with Vera (Z-Wave)'
date: Sun, 26 Oct 2014 14:24:00 +0000
draft: false
tags: ['lifx', 'raspberrypi', 'vera']
---


A quick guide on how I got my bulbs (sort of!) connected to my Vera network. No real trickery here. And the bulbs themselves aren't yet added, instead, I focused on creating common scenes and made scenes in Vera.  
  
What's needed:  

1.  A Raspberry Pi (or other \*nix computer that you're happy with keeping on all the time)
2.  Ensure it has a static IP on your local network - otherwise if the IP keeps changing, this won't work too well. For mine, the IP is 192.168.1.221
3.  Ruby v2.0 (Raspbian has an older version, so I had to follow these instructions to get 2.0 installed: ([http://jam.im/blog/2013/03/03/installing-ruby-2-and-rvm-on-a-raspberry-pi/](http://jam.im/blog/2013/03/03/installing-ruby-2-and-rvm-on-a-raspberry-pi/)) (this took forever to finsih!!)

1.  sudo aptitude update
2.  sudo aptitude install git-core
3.  curl -L https://get.rvm.io | bash -s stable --ruby
4.  pi@raspberrypi ~ $ source /home/pi/.rvm/scripts/rvm

5.  Make sure root can see the new file by changing the /bin/ruby binary to point to the new ruby that is placed somewhere obscure in your own folder. For me, it: /home/cdrum/.rvm/rubies/ruby-2.1.3/bin/ruby

1.  sudo ln -s ruby /home/cdrum/.rvm/rubies/ruby-2.1.3/bin/ruby

7.  The (unofficial) Ruby HTTP API for controlling LIFX bulbs: [https://github.com/chendo/lifx-http](https://github.com/chendo/lifx-http) (and follow those instructions on getting it installed), but the long and the short of it is:

1.  rvmsudo gem install lifx-http
2.  Note here i'm using rvmsudo - this was key to ensure that the gems are installed systemwide, and that root, during the installation, gets my new ruby 2.1.x environment (otherwise you'll get complaints that LIFX requires >= Ruby 2.0.

I wrote a simple php script to help generate the Lua code (the language Vera uses) that you can paste into the Scene configuration.

  

See this URL to get my code: [https://github.com/cdrum/Lua-Builder-for-LIFX-Ruby-HTTP-API](https://github.com/cdrum/Lua-Builder-for-LIFX-Ruby-HTTP-API)

  

Running my script, you will get a result that looks similar to this:

  

In Vera, go ahead and create a scene and click on the Luup tab:  

[![](/uploads/2014-10-26_22-24-28.png)](/uploads/2014-10-26_22-24-28.png)

  
Paste the Lua code that is generated from the script above, save, reload, and now you should have a working scene that controls LIFX bulb(s)!!