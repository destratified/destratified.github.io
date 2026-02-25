+++
title = "HaOS"
date = "2026-02-24"

[taxonomies]
tags = ["HaOS", "tailscale"]
+++

# HaOS - getting ratgdo32 working over tailscale
 ## 11:43

this was a bit of a bugger and i am still not sure why it worked. but here's the process...

setup tailscale on whatever network you are trying to access first. i have the GLi-net router that has an internal tailscale app configuration that i needed to access the 2nd location. i only have starlink at that site and with their cgnat shit its the only way to get through. after the router setup and enabling the tailscale app you login and then toggle a few settings (this is key). the subnet routing for the local IP (your router subnet - in my case 192.168.8.1/24) and the option for the wan subnet (192.168.0.0 - since this is where i had setup the ratgdo wifi originally) enabled me to connect over tailscale on my phone to the garage door openers. now i just needed to get it working in HaOS.

next i was stumped. i setup my tailscale on HaOS using the link below:
<https://github.com/hassio-addons/app-tailscale/blob/main/tailscale/DOCS.md>
and selected the button ```show app on myHA``` this took me to prompt asking if i wanted to add to my HaOS at the local network IP and port to which i said yes...install, turn on the service and start it.

installed the app as per the instructions, then you had to login to tailscale, confirm the device and agree to use subnet routes. i did not need to use as an exit router, so i left that off and disabled key expiry. 

back in the haOS tailscale application page make sure the watchdog, start on boot, auto-update and show on sidebar options are all ticked on...

then i couldn't get it to work. i was able to access the ratgdo32 interface via web on any tailscale device (checked through my phone and the laptop) and connect to any device on the 192.168.0.0/24 network, so the issue had to be in the settings on HaOS. i did find a couple posts online, but they referenced a setting that did nothing for me.

eventually making it back to the settings > apps page i went to the tailscale app and configuration tab, (which somehow i'd overlooked many times before) and found a buncha semi-hidden HaOs specific tailscale settings. to see all options toggle the "Show unused optional configuration options" to on. i tried a few options in there, one at a time, with reboots in between and finally found one that worked. the following settings worked:

> Source NAT subnet routes (this was off and i set to ON)
>
This option allows subnet devices to see the traffic originating from the subnet router, and this simplifies routing configuration. To support advanced Site-to-site networking (eg. to traverse multiple networks), you can disable this functionality. When not set, this option is enabled by default.
>
Userspace networking mode (this was off, and i left it off)
>
This option allows you to enable userspace networking mode. If you need to access other clients on your tailnet from your Home Assistant instance, disable userspace networking mode, which will create a `tailscale0` network interface on your host. When not set, this option is enabled by default.

the "Source NAT subnet routes" description is confusing...sounds like it wasn't supposed to be off, by default, but it was and when i enabled and rebooted, and the userspace netwerking name description wasnt any better. but with the two changed, and a reboot of haOS i was finally able to ping my esphome ratgdo32 devices on the separate network and add them to my configuration/dashboard.

adventures in home networking for sure... maybe this will help someone else. i'm now going to try this again at a 3rd location and see if i can get that one to talk to my HaOS as well.




