+++
title = "debian - OpenRC"
date = "2026-03-25"

[taxonomies]
tags = ["debian", "openRC", "OS"]
+++

# debian - OpenRC install and tweaks
 ## 15:58
so poking around on the internet, i was trying to find some distros that are sans systemd - i didn't like the maintainer, Lennart Poettering, just pushing through the merge request from the one guy who decided he would accept the verify ID laws as truth, without consulting anyone else, and interlock it with systemd. bad move, and i think it both caught alot of developers off guard and has raised hackles on many in the community who'd disagree that this is even legal (as its yet to be decided in court whats even enforcable)...i could go on, maybe that's why you are here, maybe not.

<https://www.sambent.com/the-engineer-who-tried-to-put-age-verification-into-linux-5/>

so what are the options? plenty of distros out there come sans systemd, but what are the tradeoffs and negatives? well, i spent a good bit of time distro hopping again - even accidentally wiped the wrong partition, wiping out my cachyOS, so i'm writing this now, from my laptop running openRC Debian.  

now there are other distros, tried void, tried venom, i'm tinkering with freebsd also...gentoo (very difficult install) in search of a somewhat easy to convert distro with no drawbacks in package systems (i.e special repos, etc) and surprisingly only 2 really fit the bill.  Alpine Linux and Debian. Alpine would work on a desktop well, but it couldn't easily pickup the touchpad, so it was out (again laptop daily driver is important here), and that left Debian.

found an article by CyberSecGuru that helped lead me in the right direction <https://thecybersecguru.com/tutorials/switch-systemd-to-openrc/#comments>

and some references to the openRC conversion in the debian docs <https://wiki.debian.org/OpenRC>

so the simplistic take debian states is not how to really get this working, and is also implemented in CyberSecGuru's guide so i just followed that. 
### getting started

now will this work for you? maybe. it seemed like everytime i went through this