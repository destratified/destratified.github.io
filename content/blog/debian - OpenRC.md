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

now will this work for you? maybe. it seemed like every time i went through this i ended up using a different command, but first off, get the debian netinstall iso and boot from it, install the barebones system only, you can just add the other stuff after.  

***network settings should remain after the boot, its using the ifupdown package, so lookup whatever you may need to do to get that working (again on a laptop - so i manually set a network and password in /etc/network/interfaces file).

i had on multiple occasions the need to set ifdown and bring it back up to get apt update working.

boot from the clean install, login as root and starting at the prompt, 
enter some iteration of the below:
```shell
apt update && apt upgrade
apt remove --allow-remove-essential systemd && apt install openrc sysvinit-core orphan-sysvinit-scripts 
```

essentially, just add and remove stuff that keeps bonking on the dependencies until you get a y/n prompt and boom.

now the installer will tell you the following line to type in, you don't have any GUI or copy and paste ability from tty1, so just be careful typing things in and double check before hitting enter:
```shell
for file in /etc/rc0.d/K*; do s=`basename $(readlink "$file")` ; /etc/init.d/$s stop; done
```

it should reboot after that. i had to force shutdown with the power button, but it booted up ok and then you are able to start adding packages.

the next few steps i just followed CyberSecGuru guys guide, and then started adding packages, your needs maybe different, but my apt command was roughly as follows...
```shell
apt install build-essential git curl wget kde-plasma network-manager openssh 
```

you can add others later, but that basically pulls 700 some odd packages and with a reboot gets you into the login prompt in kde.
### nuances 
things that i consider essential are as follows:

- tailscale
- remmina -with rdp
- obsidian
- power-profile-daemon
- kitty terminal
- ssh (which worked out of the box)

tailscale was a bitch to get working, and the takeaways i have from my limited workings with 