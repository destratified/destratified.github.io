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

so the simplistic take debian states is not helpful in getting this working, and is also partially implemented in CyberSecGuru's guide anyways, so i just followed that. 
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

after boot, ifupdown and network-manager were conflicting.
```shell
apt purge ifupdown
```
after that comment out everything in the /etc/network/interfaces file and reboot.

other things that i consider essential are as follows:

- tailscale
- remmina -with rdp
- obsidian
- power-profiles-daemon
- kitty terminal
- ssh (which worked out of the box)

tailscale was a bitch to get working, and the takeaways i have from my limited workings with openRC is if its a package that runs as a service, install it from the command line, setup a file in /etc/init.d/"whatever" and add the instructions there. i loosely followed this using a few different methods, and openRC converter on the web and was able to get it working after tweaking the init file (depends- net wasnt working for network-manger, it might for ifupdown but i wasn't on an eth connection).

so here's my working /etc/init.d/tailscale file...
```shell
#!/sbin/openrc-run

name=Tailscale
description="Tailscale node agent"
supervisor="supervise-daemon"
command="/usr/bin/tailscaled"
command_args="--statedir /var/lib/tailscale"

depend() {
	after network-online 
	use network-online 
}

start() {
    ebegin "Starting Tailscale"
    /usr/sbin/tailscaled --state=/var/lib/tailscale/tailscaled.state
    eend $?
}

stop() {
    ebegin "Stopping Tailscale"
    killall tailscaled
    eend $?
}

status() {
    if pgrep tailscaled > /dev/null; then
        ebegin "Tailscale is running"
        eend 0
    else
        ebegin "Tailscale is not running"
        eend 1
    fi
}
```

if i remember correctly, some of this was borrowed from tailscale-openrc script in Alpine, some of it was digested using the <http://openrc.run/> website and i was really skeptical that it would even work, but it did!

lastly you need to chmod it, add to openrc-run with the following command and start it, or reboot up to you.
```shell
sudo chmod +x /etc/init.d/tailscale
sudo rc-update add tailscale default
sudo rc-service tailscale start
```

then, give it a shot with `sudo tailscale up` and you should get the login prompt if everything went ok. those last commands came from <https://peet.io/blog/0004-tailscale-on-alpine-linux> which i'd referenced in getting the init script up and running, but kept having the net-depends error...

tested it out over numerous reboots and running a remote remmina from a phone hotspot to make sure it actually worked with the home network connection and all was well.

the power-profiles-daemon also was non-functional on boot and that was a little easier guide to follow that i pulled from Alpine linux:
<https://github.com/h8d13/ppd-openrc>

i cloned the repo and followed the instructions, replacing apk and doas lingo as needed:
```shell
sudo apt install power-profiles-daemon
```

this downloads the daemon, possibly re-installs, if its already there, then i confirmed the file location with the whereis command:
```shell
whereis power-profiles-daemon
```

knowing that location matched the instructions in the github directions i just followed the rest of the guide:
```shell
sudo cp power-profiles-daemon.initd /etc/init.d/power-profiles-daemon
sudo chmod +x /etc/init.d/power-profiles-daemon

sudo cp power-profiles-daemon.confd /etc/conf.d/power-profiles-daemon
sudo chmod +x /etc/conf.d/power-profiles-daemon

sudo cp ppd-profile /usr/bin/ppd-profile
sudo chmod +x /usr/bin/ppd-profile

sudo rc-update add power-profiles-daemon default
sudo rc-service power-profiles-daemon start

sudo ppd-profile balanced
```

on reboot the power profiles was no longer greyed out in the settings

remmina, kitty, ssh all worked outta the box, obsidian i installed the deb file from the obsidian website, and the only thing i seem to have to troubleshoot is getting sound to play over bluetooth (wireless bluetooth headphones do not appear under sounds > outputs, but play fine over the speakers). i will update once i get that working...

key takeaways? holy shit does the same system boot way faster! digestible output in the boot sequence, systemd is a monolith and the memory footprint is huge comparatively. little bit of work to get it all done, but you have the advantages of the full debian repos over the limited proprietary ones in Devuan for instance. i'd love to see an arch distro just up and tell systemd to fuckoff. the artix linux is lacking (cant boot video on my laptop without the nomodeset kernel line variable) and has a proprietary repo scenario as well, so i think i might dig into that next, but arch is significantly intertwined with systemd it appears. which is a major bummer, as pacman is by far the best package manager in linux oses, but whatever...also Cosmic-Desktop (system76) if you are listening, get off systemd.

## bluetooth fix...not hard
thanks to a forum recommending the debian forums ;) i went to <https://wiki.debian.org/BluetoothUser/a2dp> and under pre-configuration step one did the following:
```bash
sudo apt install pulseaudio-module-bluetooth
```

with a quick system reboot i was listening to audio on my wireless bluetooth Sony WH1000-XM5! booyah.

now i need to figure out why my media / play buttons have disappeared in my browser