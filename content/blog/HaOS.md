+++
title = "HaOS"
date = "2026-02-24"

[taxonomies]
tags = ["HaOS", "tailscale"]
+++

# HaOS - getting ratgdo32 working over tailscale
 ## 11:43

this was a bit of a bugger and i am still not sure why it worked. but here's the process...

setup tailscale on whatever network you are trying to access first. i have the GLi-net router that has an internal tailscale app configuration that i needed to access the 2nd location. i only have starlink at that site and with their cgnet shit its the only way to get through. after the router setup and enabling the tailscale app you login and then toggle a few settings (key). the subnet routing for the local IP (your router subnet) and the option for the wan subnet enabled me to connect over tailscale on my phone to the garage door openers.

next i was stumped. i setup my tailscale on HaOS using the link below:
<https://github.com/hassio-addons/app-tailscale/blob/main/tailscale/DOCS.md>
and selected the button ```show app on myHA``` this took me to prompt asking if i wanted to add to my HaOS at the local network IP and port to which i said yes...install, turn on the service and start it.

install the app as per the instructions, login and agree to use subnet routes.

you must confirm the subnet route through your tailscale manager hub, i did not need to use as an exit router, so i left that off and diabled key expiry. 

back in the haOS tailscale application page make sure the watchdog, start on boot, auto-update and show on sidebar...

i tried everything to get it working through integrations and was stumped i couldn't get it to connect. eventually making it back to the app page i went to the configuration tab, (which somehow i'd overlooked many times before). and found a buncha semi-hidden HaOs specific tailscale settings.  i tried a few, one at a time, with reboots in between and finally found one that worked. to see all options toggle the "Show unused optional configuration options" to on, that is where i found the below.
```
Source NAT subnet routes (this was off and i set to ON)

This option allows subnet devices to see the traffic originating from the subnet router, and this simplifies routing configuration. To support advanced Site-to-site networking (eg. to traverse multiple networks), you can disable this functionality. When not set, this option is enabled by default.

Userspace networking mode (this was off, and i left it off)

This option allows you to enable userspace networking mode. If you need to access other clients on your tailnet from your Home Assistant instance, disable userspace networking mode, which will create a `tailscale0` network interface on your host. When not set, this option is enabled by default.
```

the "Source NAT subnet routes" description is confusing...sounds like it wasn't supposed to be off, by default, but it was and when i enabled and rebooted, my esphome ratgdo32 devices were able to be accessed and added.

adventures in home networking for sure... maybe this will help someone else. i'm now going to try this again at a 3rd location and see if i can get that one to talk to my HaOS as well.




