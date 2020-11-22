---
title: "Fun With Pi-hole and Raspberry Pi"
date: 2020-11-22T12:49:00-06:00
tags : [ "dev", "pi-hole", "raspberry-pi" ]
draft: false
showpagemeta: false
showcomments: false
---

A Raspberry Pi sure is a handy thing to have around - not only to tinker with but also when you need a 24/7 server on the cheap to do things like cron jobs, act as motions detectors - or in my case, make a DNS Sinkhole with [Pi-hole](https://pi-hole.net/). After setting this up, not only am I enjoying quicky disposing of ads, but also the faster loading of web pages. Externally served ads can drastically increase page load times, and are frequenly misued and abused. Also, an ad-blocking browser extention has to let the page being loading before pruning ads - but blocking at the DNS level ensures the ad, and it's external scripts, never make it to the page in the first place - it's so much faster. I love it. 

Also, much like using [Little Snitch](https://obdev.at/products/littlesnitch/index.html) - monitoring the Pi-hole logs is truly eye opening. It's unreal how much silent network activity you have going on with your computers and devices.

Anyways - there are plenty of tutorials on setting Pi-hole up on a Raspberry Pi - just pick one, they are all basically the same. What I want to to here is just give you some tips - so let's get to it.

First off, I just bought [this kit](https://www.amazon.com/gp/product/B07V9P2ZSB/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&th=1) ad it had everything I needed. Also, having a USB keyboard and mouse is super handy, and a TV with an HDMI port will work if you don't have a monitor. With a duo monitor setup you can read your instructions on one while connecting to the Pi with the other. 

<figure class="">
  <picture>
    <source data-srcset="/img/pi-setup.webp" type="image/webp">
    <img data-src="/img/pi-setup.jpeg" alt="A lovely mess" class="lazyload">
  </picture>
</figure>

Now for some tips:

### You Might Not Need to Flash the SD Card ###
If you bought a kit like I did, it will already have Pi's NOOBs operating system loaded, which, on-boot, will ask you what flavor of Rasperry Pi to install. Since I don't need a UI and will just ssh into the Pi after setup, I went with the Lite OS. 

### Enabling SSH ###
Many directions say to copy a file into the ssh folder, but here's what I did instead:

```
sudo systemctl enable ssh
sudo systemctl start ssh
```
Now the sytem is started and will re-start after a shutdown/re-boot.

### The AT&T Pacer Routers Suck ###
Installing Pi-hole couldn't have been easier - and getting the entire network to route its DNS though it _should_ be easy. But my Pace Plc 5268AC modem/router doesn't let you change the DNS servers, nor does it have a passthrough option, or bridge mode. Hours of Googling ensue - and what I found was this.

You can employ some trickery that involves putting _your router_ into DMZplus mode via the ATT router UI, so that traffic can be routed to it. I did this, but it made no difference. My computers where still using ATTs crappy DNS servers instead of the Pi-hole, even though _my router_ had the DNS set for the Pi-hole IP.  

> To check what DNS server your *nix computer is using:<br>
 `scutil --dns | grep 'nameserver\[[0-9]*\]'`

Then I read some info about how you can limit the DHCP addresses the ATT router hands out to 1, then shut off all your devices and then start up _your router_ so it receives the only DHCP that is doled out, and then any other devices will use _your router_ for dhcp (or Pi-hole's DHCP, which you can turn on via the web interface). In theory this would persist across reboots as routers like to cache what IPs they use per device.

While this seemed promising, and I'm always willing to f*ck things up in the name of learning, I decided to stop here b/c based on what I was reading about these ATT routers, resetting back to defaults _doesn't always work_ - and the last thing my household needs is to lose internet during a pandemic. 

What I did manage to do through the ATT router UI is give the Pi-hole a static IP so that it is always found by our computers. From here I just set each invididual computer/device to use that IP for its DNS server. Note that in iOS you can assign DNS servers for each network you join - so our phones now get to use Pi-hole while on the home network (I'm sure Android has the same option). The only downside is that our laptops will need a manual change of the DNS each time we leave/return - but that's a small price to pay and I'm fine with that, for now at least. 

I am thinking of upgrading our Internet, so I will def be taking this router experience into consideration when I do that.

### Show Hostnames instead of IPs in the Pi-Hole Admin Site ###
One weird thing I noticed in the Pi-Hole admin website was that only 1 of our laptops (our oldest, running Mojave) was actually showing its hostname in the admin site - all other devices were just IPs. Weird. Was that b/c Mojave did things differently that other OSs? Fortunately the fix was pretty easy - in the Pi-hole admin site, just navigate to "Advanced DNS settings" (/admin/settings.php?tab=dns), turn on "Conditional forwarding," and enter the correct settings for your network's IP range and DHCP server IP (in my case, the ATT router) - and voila, now we have hostnames across the board:

<figure class="">
  <picture>
    <source data-srcset="/img/with-conditional-forwarding.webp" type="image/webp">
    <img data-src="/img/with-conditional-forwarding.png" alt="With conditional forwarding on" class="lazyload">
  </picture>
</figure>

### Things like movie trailers in the IMDB app might not play ###
Just about every trailer in the IMDB app has an add in front of it. Since the ad never gets served, whatever triggers the trailer to actually play never gets triggered. Cool? It seems to work fine on imdb.com fwiw (basically a blank ad seems to play for 15 secs or whatever, and then the trailer starts), so just head to the website. In the app, if you click on the ad, you'll see a blank page :) but the URL looks from amazon ad services - so white-listing the domain could be risky. On the other hand, the YouTube ap seems fine, but plays ads as usual - probably b/c...

### If the websites uses it's own ad platform on it's own domain, it won't get blocked ###
For example - it would be lovely if this blocked ads on Facebook and the like, but in order to block ads on FB you'd have to blacklist FB, which means no FB. 

<hr/>

In all, I gotta say, this was super fun and I learned a thing or 2 about networking and related devices. It's seems to have really sped up our browing at home, and has me thinking about using Pi-hole with OpenVPN for when we're not at home. And should we make changes to our home network, I know what to look for and what questions to ask equipment-wise. Cheers 🍺